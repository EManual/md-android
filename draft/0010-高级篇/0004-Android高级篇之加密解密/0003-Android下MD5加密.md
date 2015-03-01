目前做一个项目，用到把用户输入的密码进行md5加密，于是google了下，网上资料很多，但是测试了下总是跟网站的md5加密对不起来， 后来才知道是编码方式不对，我于是自己写一个吧。哎有些事情总是想偷懒，没想到去找东西的时间比要自己写出来花费更长时间。
声明变量
```  
private static final char HEX_DIGITS[] = { '0', '1', '2', '3', '4', '5',
		'6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };
public static String toHexString(byte[] b) { // String to byte
	StringBuilder sb = new StringBuilder(b.length * 2);
	for (int i = 0; i < b.length; i++) {
		sb.append(HEX_DIGITS[(b[i] & 0xf0) >>> 4]);
		sb.append(HEX_DIGITS[b[i] & 0x0f]);
	}
	return sb.toString();
}
public String md5(String s) {
	try {
		// Create MD5 Hash
		MessageDigest digest = java.security.MessageDigest
				.getInstance("MD5");
		digest.update(s.getBytes());
		byte messageDigest[] = digest.digest();

		return toHexString(messageDigest);
	} catch (NoSuchAlgorithmException e) {
		e.printStackTrace();
	}
	return "";
}
```