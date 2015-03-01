最近感觉android系统自带的输入法很不好用，打字的时候老是点击错误，于是把android系统自带的输入法从系统里直接干掉了(必须破解后)，安装了搜狗输入法,感觉用搜狗的输入法输入文字非常快，中英文，标点符号切换都很快。但是如果手机每次重启的时候搜狗输入法启动的很慢，有时候就是启动不起来，所以无法输入文件信息。于是我就写下了下面的程序，开始自动启动搜狗输入法进程:
下面我们就先在android Manifest.xml设置一下权限：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="sunny.app"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
    </application>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
</manifest>
```
我们现在就要看看主代码了，大家可要仔细的看呀：
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
public class BootBroadcastReceiver extends BroadcastReceiver {
	static final String ACTION = "android.intent.action.BOOT_COMPLETED";
	int ActionFlag = 0;
	public void onReceive(Context context, Intent intent) {
		if (intent.getAction().equals(ACTION)) {
			Intent helloIntent = new Intent(context, StartSouGou.class);
			helloIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
			context.startService(helloIntent);
			ActionFlag = -1;
		}
	}
}
public class StartSouGou extends Service {
	public IBinder onBind(Intent arg0) {
		return null;
	}
	public int onStartCommand(Intent intent, int flags, int startId) {
		Runtime runtime = Runtime.getRuntime();
		try {
			runtime.exec("com.sohu.inputmethod.sogou");
		} catch (IOException e) {
		}
		return super.START_NOT_STICKY;
	}
	public void onDestroy() {
		super.onDestroy();
	}
}
```