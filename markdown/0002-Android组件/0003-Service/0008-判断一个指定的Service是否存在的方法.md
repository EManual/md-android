这是一个判断一个指定的Service是否存在的方法。
它被用于监视一个Service是否由于已经运转，如果由于各种原因Service已经被停止了。
这是在重新启动指定Service。
它被用于一个Application中有多个Service。
```  
public static boolean isServiceExisted(Context context, String className) {
	ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
	List<ActivityManager.RunningServiceInfo> serviceList = activityManager.getRunningServices(Integer.MAX_VALUE);
	if(!(serviceList.size() > 0)) {
		return false;
	}
	for(int i = 0; i < serviceList.size(); i++) {
		RunningServiceInfo serviceInfo = serviceList.get(i);
		ComponentName serviceName = serviceInfo.service;
		if(serviceName.getClassName().equals(className)) {
			return true;
		}
	} 
	return false;
}
```