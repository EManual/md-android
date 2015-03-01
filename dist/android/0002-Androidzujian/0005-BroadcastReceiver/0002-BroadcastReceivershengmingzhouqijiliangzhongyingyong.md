#### 一：Android 广播的生命周期  
一个广播接收者有一个回调方法：void onReceive(Context curContext, Intent broadcastMsg)。当一个广播消息到达接收者时，Android调用它的onReceive()方法并传递给它包含消息的Intent对象。广播接收者被认为仅当它执行这个方法时是活跃的。当onReceive()返回后，它是不活跃的。 
有一个活跃的广播接收者的进程是受保护的，不会被杀死。但是系统可以在任何时候杀死仅有不活跃组件的进程，当占用的内存别的进程需要时。 
这带来一个问题，当一个广播消息的响应时费时的，因此应该在独立的线程中做这些事，远离用户界面其它组件运行的主线程。如果onReceive()衍生线程然后返回，整个进程，包括新的线程，被判定为不活跃的（除非进程中的其它应用程序组件是活跃的），将使它处于被杀的危机。解决这个问题的方法是onReceive()启动一个服务，及时服务做这个工作，因此系统知道进程中有活跃的工作在做。
Broadcast Receive为广播接收器，它和事件处理机制类似，只不过事件的处理机制是程序组件级别的，广播处理机制是系统级别的。 
Broadcast Receiver用于接收并处理广播通知（broadcast announcements）。多数的广播是系统发起的，如地域变换、电量不足、来电来信等。程序也可以播放一个广播。程序可以有任意数量的 broadcast receivers来响应它觉得重要的通知。broadcast receiver可以通过多种方式通知用户：启动activity、使用NotificationManager、开启背景灯、振动设备、播放声音等，最典型的是在状态栏显示一个图标，这样用户就可以点它打开看通知内容。 
通常我们的某个应用或系统本身在某些事件（电池电量不足、来电来短信）来临时会广播一个Intent出去，我们可以利用注册一个Broadcast Receiver来监听到这些Intent并获取Intent中的数据。
#### 二：Android 广播应用 
android中，不同进程之间传递信息要用到广播，可以有两种方式来实现。
第一种方式：在Manifest.xml中注册广播，是一种比较推荐的方法，因为它不需要手动注销广播（如果广播未注销，程序退出时可能会出错）。 
具体实现在Manifest的application中添加：
```  
<receiver android:name=".mEvtReceiver"> 
	<intent-filter>
		<action android:name="android.intent.action.BOOT_COMPLETED" />
	</intent-filter>
</receiver> 
```
上面两个android:name分别是广播名和广播的动作（这里的动作是表示系统启动完成），如果要自己发送一个广播，在代码中为：
```  
Intent i = new Intent("android.intent.action.BOOT_COMPLETED"); 
sendBroadcast(i);
```
这样，广播就发出去了，然后是接收。接收可以新建一个类，继承至BroadcastReceiver，也可以建一个BroadcastReceiver的实例，然后得写onReceive方法，实现如下：
```  
protected BroadcastReceiver mEvtReceiver = new BroadcastReceiver() {
	@Override
	public void onReceive(Context context, Intent intent) {
		String action = intent.getAction();
		if (action.equals("android.intent.action.BOOT_COMPLETED")) {
			// Do something
		}
	}
}; 
```
完整实例：
```   
<?xml version="1.0" encoding="utf-8"?> 
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
	package="android.basic.lesson21"
	android:versionCode="1"
	android:versionName="1.0">
	<uses-sdk android:minSdkVersion="8" />
	<application android:icon="@drawable/icon" android:label="@string/app_name">
	<activity android:name=".MainBroadcastReceiver"
		android:label="@string/app_name">
		<intent-filter>
			<action android:name="android.intent.action.MAIN" />
			<category android:name="android.intent.category.LAUNCHER" />
		</intent-filter>
	</activity>
	<receiver android:name=".HelloBroadReciever">
		<intent-filter>
			<action android:name="android.basic.lesson21.Hello88" />
		</intent-filter>
	</receiver>
	</application>
</manifest> 
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class MainBroadcastReceiver extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button b1 = (Button) findViewById(R.id.Button01);
		b1.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				// 定义一个intent
				Intent intent = new Intent().setAction(
						"android.basic.lesson21.Hello88").putExtra("yaoyao",
						"yaoyao is 189 days old ,27 weeks -- 2010-08-10");
				System.out.println("sendBroadcast");
				// 广播出去
				sendBroadcast(intent);
			}
		});
	}
}
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;
public class HelloBroadReciever extends BroadcastReceiver {
	public HelloBroadReciever() {
		System.out.println("HelloBroadReciever");
	}
	// 如果接收的事件发生
	@Override
	public void onReceive(Context context, Intent intent) {
		// 对比Action决定输出什么信息
		if (intent.getAction().equals("android.intent.action.BOOT_COMPLETED")) {
			Log.i("HelloBroadReciever",
					"BOOT_COMPLETED !!!!!!!!!!!!!!!!!!!!!!!!!");
		}
		if (intent.getAction().equals("android.basic.lesson21.Hello88")) {
			Log.i("HelloBroadReciever",
					"Say Hello to Yaoyao !!!!!!!!!!!!!!!!!!!!!!!!!");
			Log.i("HelloBroadReciever", intent.getStringExtra("yaoyao"));
		}
		Log.i("HelloBroadReciever", "onReceive**********");
		// 播放一首音乐
		// MediaPlayer.create(context, R.raw.babayetu).start();
	}
}
```
第二种方式，直接在代码中实现，但需要手动注册注销，实现如下：（以短信接收为例） 
```  
IntentFilter filter = new IntentFilter(); 
filter.addAction("android.provider.Telephony.SMS_RECEIVED"); 
registerReceiver(mEvtReceiver, filter); //这时注册了一个recevier，名为mEvtReceiver，然后同样用上面的方法以重写onReceiver 
```
最后在程序的onDestroy中要注销广播，实现如下： 
```  
@Override
public void onDestroy() {
	super.onDestroy();
	unregisterReceiver(mPlayerEvtReceiver);
}
```
以短信接收为例： 
```  
<?xml version="1.0" encoding="utf-8"?> 
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
	package="android.basic.lesson21"
	android:versionCode="1"
	android:versionName="1.0">
	<uses-sdk android:minSdkVersion="5" />
	<application android:icon="@drawable/icon" android:label="@string/app_name">
	<activity android:name=".MainBroadcastReceiver"
		android:label="@string/app_name">
		<intent-filter>
			<action android:name="android.intent.action.MAIN" />
			<category android:name="android.intent.category.LAUNCHER" />
		</intent-filter>
	</activity>
	</application>
	<uses-permission android:name="android.permission.RECEIVE_SMS"/>
</manifest>
```
增加一个短信权限<uses-permission android:name="android.permission.RECEIVE_SMS"/> 
```  
import android.app.Activity;
import android.content.IntentFilter;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
public class MainBroadcastReceiver extends Activity {
	 /** Called when the activity is first created. */
	HelloBroadReciever br;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button b1 = (Button) findViewById(R.id.Button01);
		b1.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				Log.i("click", "OnClick");
				br = new HelloBroadReciever();
				IntentFilter filter = new IntentFilter();
				filter.addAction("android.provider.Telephony.SMS_RECEIVED");
				registerReceiver(br, filter);
			}
		});
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		unregisterReceiver(br);
		Log.i("br", "onDestroy");
	}
}
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;
public class HelloBroadReciever extends BroadcastReceiver {
	public HelloBroadReciever() {
		System.out.println("HelloBroadReciever");
	}
	// 如果接收的事件发生
	@Override
	public void onReceive(Context context, Intent intent) {
		// 对比Action决定输出什么信息
		if (intent.getAction()
				.equals("android.provider.Telephony.SMS_RECEIVED")) {
			Log.i("HelloBroadReciever",
					"SMS_RECEIVED !!!!!!!!!!!!!!!!!!!!!!!!!");
		}
		Log.i("HelloBroadReciever", "onReceive**********");
	}
}
```
先按开始键注册广播，然后在Emulator Control设置发短信如图： 
```  
按Send,这时HelloBroadReciever的onReceive响应 
输出SMS_RECEIVED !!!!!!!!!!!!!!!!!!!!!!!!! 
onReceive**********"信息。
``` 
一个广播接收者有一个回调方法：void onReceive(Context curContext, Intent broadcastMsg)。当一个广播消息到达接收者是，Android调用它的onReceive()方法并传递给它包含消息的Intent对象。广播接收者被认为仅当它执行这个方法时是活跃的。当onReceive()返回后，它是不活跃的。 
有一个活跃的广播接收者的进程是受保护的，不会被杀死。但是系统可以在任何时候杀死仅有不活跃组件的进程，当占用的内存别的进程需要时。 
这带来一个问题，当一个广播消息的响应时费时的，因此应该在独立的线程中做这些事，远离用户界面其它组件运行的主线程。如果onReceive()衍生线程然后返回，整个进程，包括新的线程，被判定为不活跃的（除非进程中的其它应用程序组件是活跃的），将使它处于被杀的危机。解决这个问题的方法是onReceive()启动一个服务，及时服务做这个工作，因此系统知道进程中有活跃的工作在做。