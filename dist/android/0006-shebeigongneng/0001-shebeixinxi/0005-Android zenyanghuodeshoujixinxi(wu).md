1.2.5 获取运行时信息
运行时的一些信息，包括后台运行的Service、Task，以及进程信息。
1.2.5.1 获取正在运行的Service信息
可以通过调用context.getSystemService(Context.ACTIVITY_SERVICE)获取 ActivityManager，进而通过系统提供的方法getRunningServices(int maxNum)获取正在运行的服务列表(RunningServiceInfo)，再对其结果进一步分析，得到服务对应的进程名及其他信息，实现的关键代码如下：
```  
// 正在运行的服务列表
public static String getRunningServicesInfo(Context context) {
	StringBuffer serviceInfo = new StringBuffer();
	final ActivityManager activityManager = (ActivityManager) context
			.getSystemService(Context.ACTIVITY_SERVICE);
	List<RunningServiceInfo> services = activityManager
			.getRunningServices(100);
	Iterator<RunningServiceInfo> l = services.iterator();
	while (l.hasNext()) {
		RunningServiceInfo si = (RunningServiceInfo) l.next();
		serviceInfo.append("pid: ").append(si.pid);
		serviceInfo.append(" process: ").append(si.process);
		serviceInfo.append(" service: ").append(si.service);
		serviceInfo.append(" crashCount: ").append(si.crashCount);
		serviceInfo.append(" clicentCount: ").append(si.clientCount);
		serviceInfo.append(" activeSince:").append(
				ToolHelper.formatData(si.activeSince));
		serviceInfo.append(" lastActivityTime: ").append(
				ToolHelper.formatData(si.lastActivityTime));
		serviceInfo.append(" ");
	}
	return serviceInfo.toString();
}
```
上述代码调用activityManager.getRunningServices(100)获取正在运行的服务，并依次遍历得到每个服务对应的pid，进程等信息。
1.2.5.2 获取正在运行的Task信息
获取正在运行的Task信息调用的是activityManager.getRunningTasks(int maxNum)来获取对应的正在运行的任务信息列表(RunningTaskInfo)，进而分析、显示任务信息，其关键代码如下：
```  
public static String getRunningTaskInfo(Context context) {
	StringBuffer sInfo = new StringBuffer();
	final ActivityManager activityManager = (ActivityManager) context
			.getSystemService(Context.ACTIVITY_SERVICE);
	List<RunningTaskInfo> tasks = activityManager.getRunningTasks(100);
	Iterator<RunningTaskInfo> l = tasks.iterator();
	while (l.hasNext()) {
		RunningTaskInfo ti = (RunningTaskInfo) l.next();
		sInfo.append("id: ").append(ti.id);
		sInfo.append(" baseActivity: ").append(
				ti.baseActivity.flattenToString());
		sInfo.append(" numActivities: ").append(ti.nnumActivities);
		sInfo.append(" numRunning: ").append(ti.numRunning);
		sInfo.append(" description: ").append(ti.description);
		sInfo.append(" ");
	}
	return sInfo.toString();
}
```
上述代码调用系统提供的activityManager.getRunningTasks(100)方法获取任务列表，依次获取对应的id等信息，运行效果如图22。从图中显示可以看出，获取手机上正在运行的Task的列表和其对应的进程信息，这对用户了解设备运行情况非常有用。
1.2.5.3 获取正在运行的进程信息
该段程序是通过CMD Execute的方式来运行系统命令。关键代码如下：
```  
public static String fetch_process_info() {
	Log.i("fetch_process_info", "start. . . . ");
	String result = null;
	CMDExecutr cmdexe = new CMDExecute();
	try {
		String[] args = { "/system/bin/top", "-n", "1" };
		result = cmdexe.run(args, "/system/bin/");
	} catch (IOException ex) {
		Log.i("fetch_process_info", "ex=" + ex.toString());
	}
	return result;
}
```
通过这个功能可以非常详细地了解到正在运行的进程和各个进程所消耗的资源情况。
1.2.6 文件浏览器
文件浏览器的这个功能，用户可以遍历浏览整个文件系统，以便更好地了解手机设备状况。在主界面单击最后一行将执行下列代码：
```  
case 4:
intent.setClass(eoeInfosAssistant.this, FSExplorer.class);
startActivity(intent);
break;
```
对于如何进入子目录，并获取和显示其内部的文件夹和文件，也就是单击每行时响应的实现，代码如下：
```  
@Override
public void onItemClick(AdapterView<?> parent, View v, int position, long id) {
	Log.i(TAG, "item clicked! [" + position + "]");
	if (position == 0) {
		path = "/";
		refreshListItems(path);
	} else if (position == 1) {
		goToParent();
	} else {
		path = (String) list.get(position).get("path");
		File file = new File(path);
		if (file.isDirectory())
			refreshListItems(path);
		else
			Toast.makeText(FSExplorer.this, getString(R.string.is_file),
					Toast.LENGTH_SHORT).show();
	}
}
```