由于Android应用没有像web开发中的session机制，所以采用PHPSESSID的方式，是没有办法获取客户端登录状态的。
这种情况下，如何在用户登录后，服务器端获取用户登录状态并保持，就必须采用一种“握手”的方式。
每个手机都有自己的IMEI号，那么能不能通过这个标识去做认证呢？
经过试验，答案是可以。
客户端在请求服务器端的时候，请求参数为 IMEI （param 1）及  IMEI&UA （param 2）经过加密的字符串；服务器端对客户端传递的两个参数进行解密，比对两个IMEI值是否相同。如果相同，返回token给客户端，以后每次客户端请求服务器端的时候，都携带该token。这样服务器就可以获取用户登录状态了。
这里，我采用的DES加密的方式，由于PHP和Java的DES加密是有差异的，所以单独进行处理。
```  
import java.security.Key;
import java.security.SecureRandom;
import java.security.spec.AlgorithmParameterSpec;
import javax.crypto.Cipher;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;
import javax.crypto.spec.IvParameterSpec;
import com.sun.org.apache.xml.internal.security.utils.Base64;
public class Des2 {
	public static final String ALGORITHM_DES = "DES/CBC/PKCS5Padding";
	[Tags]/**
	 [Tags]* DES算法,加密
	 [Tags]* @param data 待加密字符串
	 [Tags]* @param key 加密私钥,长度不能够小于8位
	 [Tags]* @return 加密后的字节数组,一般结合Base64编码使用
	 [Tags]* @throws CryptException 异常
	 [Tags]*/
	public static String encode(String key, String data) throws Exception {
		return encode(key, data.getBytes());
	}
	[Tags]/**
	 [Tags]* DES算法,加密
	 [Tags]* @param data 待加密字符串
	 [Tags]* @param key 加密私钥,长度不能够小于8位
	 [Tags]* @return 加密后的字节数组,一般结合Base64编码使用
	 [Tags]* @throws CryptException 异常
	 [Tags]*/
	public static String encode(String key, byte[] data) throws Exception {
		try {
			DESKeySpec dks = new DESKeySpec(key.getBytes());
			SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
			// key的长度不能够小于8位字节
			Key secretKey = keyFactory.generateSecret(dks);
			Cipher cipher = Cipher.getInstance(ALGORITHM_DES);
			IvParameterSpec iv = new IvParameterSpec("12345678".getBytes());
			AlgorithmParameterSpec paramSpec = iv;
			cipher.init(Cipher.ENCRYPT_MODE, secretKey, paramSpec);
			byte[] bytes = cipher.doFinal(data);
			return Base64.encode(bytes);
		} catch (Exception e) {
			throw new Exception(e);
		}
	}
	[Tags]/**
	 [Tags]* DES算法,解密
	 [Tags]* @param data 待解密字符串
	 [Tags]* @param key 解密私钥,长度不能够小于8位
	 [Tags]* @return 解密后的字节数组
	 [Tags]* @throws Exception 异常
	 [Tags]*/
	public static byte[] decode(String key, byte[] data) throws Exception {
		try {
			SecureRandom sr = new SecureRandom();
			DESKeySpec dks = new DESKeySpec(key.getBytes());
			SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
			// key的长度不能够小于8位字节
			Key secretKey = keyFactory.generateSecret(dks);
			Cipher cipher = Cipher.getInstance(ALGORITHM_DES);
			IvParameterSpec iv = new IvParameterSpec("12345678".getBytes());
			AlgorithmParameterSpec paramSpec = iv;
			cipher.init(Cipher.DECRYPT_MODE, secretKey, paramSpec);
			return cipher.doFinal(data);
		} catch (Exception e) {
			throw new Exception(e);
		}
	}
	[Tags]/**
	 [Tags]* 获取编码后的值
	 [Tags]* @param key
	 [Tags]* @param data
	 [Tags]* @throws Exception
	 [Tags]*/
	public static String decodeValue(String key, String data) {
		byte[] datas;
		String value = null;
		try {
			if (System.getProperty("os.name") != null
					&& (System.getProperty("os.name").equalsIgnoreCase("sunos") || System
							.getProperty("os.name").equalsIgnoreCase("linux"))) {
				datas = decode(key, Base64.decode(data));
			} else {
				datas = decode(key, Base64.decode(data));
			}
			value = new String(datas);
		} catch (Exception e) {
			value = "";
		}
		return value;
	}
	[Tags]/**
	 [Tags]* test
	 [Tags]* @param key ： 12345678
	 [Tags]*/
	public static void main(String[] args) throws Exception {
		System.out.println("明：cychai ;密：" + Des2.encode("12345678", "cychai"));
	}
}
```
PHP：
```  
class DES
{
	var $key;
	var $iv; //偏移量　
	function DES($key, $iv=0)
	{
		$this->key = $key;
		if($iv == 0)
		{
			$this->iv = $key;
		}
		else
		{
			$this->iv = $iv;
		}
	}
	//加密
	function encrypt($str)
	{
		$size = mcrypt_get_block_size ( MCRYPT_DES, MCRYPT_MODE_CBC );
		$str = $this->pkcs5Pad ( $str, $size );
		$data=mcrypt_cbc(MCRYPT_DES, $this->key, $str, MCRYPT_ENCRYPT, $this->iv);
		//$data=strtoupper(bin2hex($data)); //返回大写十六进制字符串
		return base64_encode($data);
	}
	//解密
	function decrypt($str)
	{
		$str = base64_decode ($str);
		//$strBin = $this->hex2bin( strtolower($str));
		$str = mcrypt_cbc(MCRYPT_DES, $this->key, $str, MCRYPT_DECRYPT, $this->iv );
		$str = $this->pkcs5Unpad( $str );
		return $str;
	}
	function hex2bin($hexData)
	{
		$binData = "";
		for($i = 0; $i < strlen ( $hexData ); $i += 2)
		{
			$binData .= chr(hexdec(substr($hexData, $i, 2)));
		}
		return $binData;
	}
	function pkcs5Pad($text, $blocksize)
	{
		$pad = $blocksize - (strlen ( $text ) % $blocksize);
		return $text .str_repeat ( chr ( $pad ), $pad );
	}
	function pkcs5Unpad($text)
	{
	$pad = ord ( $text {strlen ( $text ) - 1} );
		if ($pad > strlen ( $text ))
		return false;
		if (strspn ( $text, chr ( $pad ), strlen ( $text ) - $pad ) ！= $pad)
		return false;
		return substr ( $text, 0, - 1 * $pad );
	}
}
$str = 'abc';
$key= '12345678';
$crypt = new DES($key);
$mstr = $crypt->encrypt($str);
$str = $crypt->decrypt($mstr);
echo  $str.' <=> '.$mstr;
```
需要注意的是： 加密的key必须为8位或者8的倍数，否则在java加密过程中会报错。