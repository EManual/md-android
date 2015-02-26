今天我们来看看怎么样才能用APN来获取手机号。
```  
 /**
 * 电信APN列表
 */
public class APNNET {
	public static String CTWAP="ctwap";
	public static String CTNET="ctnet";
}
 /**
  * 获得APN类型
  */
public class ApnUtil {
	private static Uri PREFERRED_APN_URI = Uri
			.parse("content://telephony/carriers/preferapn");
	 /**
	  * get apntype
	  * @param context
	  * @return
	  */
	public static String getApnType(Context context) {
		String apntype = "nomatch";
		Cursor c = context.getContentResolver().query(PREFERRED_APN_URI, null,
				null, null, null);
		c.moveToFirst();
		String user = c.getString(c.getColumnIndex("user"));
		if (user.startsWith(APNNET.CTNET)) {
			apntype = APNNET.CTNET;
		} else if (user.startsWith(APNNET.CTWAP)) {
			apntype = APNNET.CTWAP;
		}
		return apntype;
	}
}
```
获得手机号码的话可以传IMSI码到指定接口，接口地址不方便说。但可以透露一点，必须走CTWAP，这也是判断APN类型的原因，发现很多应用如果APN是走代理的话就不能联网，那么再介绍一下用APN设置网络的代理信息。
```  
Cursor c = context.getContentResolver().query(PREFERRED_APN_URI, null,
		null, null, null);
c.moveToFirst();
String proxy = c.getString(c.getColumnIndex("proxy"));
if (!"".equals(proxy) && proxy != null) {
	Properties prop = System.getProperties();
	System.getProperties().put("proxySet", "true");
	prop.setProperty("http.proxyHost",
			c.getString(c.getColumnIndex("proxy")));
	prop.setProperty("http.proxyPort",
			c.getString(c.getColumnIndex("port")));
	String authentication = c.getString(c.getColumnIndex("user")) + ":
			+ c.getString(c.getColumnIndex("password"));
	String encodedLogin = Base64.encode(authentication);
	uc.setRequestProperty("Proxy-Authorization", " BASIC
			+ encodedLogin);
}
c.close();
```