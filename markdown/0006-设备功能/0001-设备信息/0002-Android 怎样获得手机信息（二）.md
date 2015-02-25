1.2.2.2 系统信息
在Android中，想要获取系统信息，可以调用其提供的方法System.getProperty(propertyStr)，而系统信息诸如用户根目录(user.home)等都可以通过这个方法获取，实现代码如下：
```  
public static StringBuffer buffer = null;
private static String initProperty(String description, String propertyStr) {
	if (buffer == null) {
		buffer = new StringBuffer();
	}
	buffer.append(description).append(":");
	buffer.append(System.getProperty(propertyStr)).append(" ");
	return buffer.toString();
}
private static String getSystemProperty() {
	buffer = new StringBuffer();
	initProperty("java.vendor.url", "java.vendor.url");
	initProperty("java.class.path", "java.class.path");
	return buffer.toString();
}
```
上述代码主要是通过调用系统提供的System.getProperty方法获取指定的系统信息，并合并成字符串返回。
1.2.2.3 运营商信息
运营商信息中包含IMEI、手机号码等，在Android中提供了运营商管理类(TelephonyManager)，可以通过TelephonyManager来获取运营商相关的信息，实现的关键代码如下：
```  
public static String fetch_tel_status(Context cx) {
	String result = null;
	TelephonyManager tm = (TelephonyManager) cx
			.getSystemService(Context.TELEPHONY_SERVICE);
	String str = " ";
	str += "DeviceId(IMEI) = " + tm.getDeviceId() + " ";
	str += "DeviceSoftwareVersion = " + tm.getDeviceSoftwareVersion() + " ";
	int mcc = cx.getResources().getConfiguration().mcc;
	int mnc = cx.getResources().getConfiguration().mnc;
	str += "IMSI MCC (Mobile Country Code): " + String.valueOf(mcc) + " ";
	str += "IMSI MNC (Mobile Network Code): " + String.valueOf(mnc) + " ";
	result = str;
	return result;
}
```
在上述的代码中，首先调用系统的getSystemService(Context.TELEPHONY_SERVICE)方法获取一个TelephonyManager对象tm，进而调用其方法getDeviceId()获取DeviceId信息，调用getDeviceSoftware Version()获取设备的软件版本信息等。
1.2.3 查看硬件信息
1.2.3.1 获取CPU信息
可以在手机设备的/proc/cpuinfo中获取CPU信息，调用CMDEexecute执行系统的cat的命令，取/proc/cpuinfo的内容，显示的就是其CPU信息，实现代码如下：
```  
public static String fetch_cpu_info() {
	String result = null;
	CMDExecute cmdexe = new CMDExecute();
	try {
		String[] args = { "/system/bin/cat", "/proc/cpuinfo" };
		result = cmdexe.run(args, "/system/bin/");
		Log.i("result", "result=" + result);
	} catch (IOException ex) {
		ex.printStackTrace();
	}
	return result;
}
```
上述代码使用CMDExecute,调用系统中的"/system/bin/cat"命令查看"/proc/cpuinfo"中的内容，即可得到CPU信息。