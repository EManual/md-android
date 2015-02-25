1.2.3.2 获取内存信息
获取内存信息的方法和获取CPU信息的实现差不多，可以读取/proc/meminfo信息，另外还可以通过getSystemService(Context.ACTIVIT_SERV-ICE)获取ActivityManager.MemoryInfo对象，进而获取可用内存信息，主要代码如下：
```  
[Tags]/**
 [Tags]* 系统内存情况查看
 [Tags]*/
public static String getMemoryInfo(Context context) {
	StringBuffer memoryInfo = new StringBuffer();
	final ActivityManager activityManager = (ActivityManager) context
			.getSystemService(Context.ACTIVITY_SERVICE);
	ActivityManager.MemoryInfo outInfo = new ActivityManager.MemoryInfo();
	activityManager.getMemoryInfo(outInfo);
	memoryInfo.append(" Total Available Memory :")
			.append(outInfo.availMem >> 10).append("k");
	memoryInfo.append(" Total Available Memory :")
			.append(outInfo.availMem >> 20).append("k");
	memoryInfo.append(" In low memory situation:")
			.append(outInfo.lowMemory);
	String result = null;
	CMDExecute cmdexe = new CMDExecute();
	try {
		String[] args = { "/system/bin/cat", "/proc/meminfo" };
		result = cmdexe.run(args, "/system/bin/");
	} catch (IOException ex) {
		Log.i("fetch_process_info", "ex=" + ex.toString());
	}
	return (memoryInfo.toString() + " " + result);
}
```
上述代码中首先通过ActivityManager对象获取其可用的内存，然后通过查看“/proc/meminfo”内容获取更详细的信息。
1.2.3.3 获取磁盘信息
手机设备的磁盘信息可以通过df命令获取，所以，这里获取磁盘信息的方法和前面类似，惟一不同的是，这个是直接执行命令，获取其命令的返回就可以了，关键代码如下：
```  
// 磁盘信息
public static String fetch_disk_info() {
	String result = null;
	CMDExecute cmdexe = new CMDExecute();
	try {
		String[] args = { "/system/bin/df" };
		result = cmdexe.run(args, "/system/bin/");
		Log.i("result", "result=" + result);
	} catch (IOException ex) {
		ex.printStackTrace();
	}
	return result;
}
```
1.2.3.4 获取网络信息
要获取手机设备的网络信息，只要读取/system/bin/netcfg中的信息就可以了，关键代码如下：
```  
public static String fetch_netcfg_info() {
	String result = null;
	CMDExecute cmdexe = new CMDExecute();
	try {
		String[] args = { "/system/bin/netcfg" };
		result = cmdexe.run(args, "/system/bin/");
	} catch (IOException ex) {
		Log.i("fetch_process_info", "ex=" + ex.toString());
	}
	return result;
}
```
1.2.3.5获取显示频信息
除了显示手机的CPU、内存、磁盘信息外，还有个非常重要的硬件，显示频。在Android中，它提供了DisplayMetrics类，可以通过getApplication Context()、getResources()、getDisplayMetrics()初始化，进而读取其屏幕宽(widthPixels)、高(heightPixels)等信息，实现的关键代码如下：
```  
public static String getDisplayMetrics(Context cx) {
	String str = "";
	DisplayMetrics dm = new DisplayMetrics();
	dm = cx.getApplicationContext().getResources().getDisplayMetrics();
	int screenWidth = dm.widthPixels;
	int screenHeight = dm.heightPixels;
	float density = dm.density;
	float xdpi = dm.xdpi;
	float ydpi = dm.ydpi;
	str += "The absolute width: " + String.valueOf(screenWidth) + "pixels ";
	str += "The absolute heightin: " + String.valueOf(screenHeight)
			+ "pixels ";
	str += "The logical density of the display. :
			+ String.valueOf(density) + " ";
	str += "X dimension : " + String.valueOf(xdpi) + "pixels per inch ";
	str += "Y dimension : " + String.valueOf(ydpi) + "pixels per inch ";
	return str;
}
```