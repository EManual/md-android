当拨打电话时，拨打电话程序会向系统发生消息，来告诉系统自己在干嘛，这里就是所谓的广播，这样做是为了方便跟其他应用程序或者底层沟通。那么如何才能让其他应用程序拿到这个消息（广播），这里就需要借助广播接收者BroadcastReceiver这个类。
BroadcastReceiver的onReceiver方法用来接收广播，当一个程序安装到系统的时候，会注册到系统中，这样就能得到系统中的各种广播或者其他有信息，然后与其他程序打交道。过滤到自己想要的广播得指定IntentFilter特定的action，还得是否有这个权限读到这个广播消息，并且必须对该广播接收者进行注册。以下是实现方式：
第一种实现方式,写在AndroidManifest.xml里
```  
<receiver android:name=".MyReceiver"> 
	<intent-filter>
		<action android:name="android.intent.action.PHONE_STATE"/>
	</intent-filter>
</receiver>
```
第二种方式是在java代码里
```  
MyReceiver myReceiver = new MyReceiver(); 
IntentFilter filter = new IntentFilter(); 
filter.addAction("android.intent.action.PHONE_STATE"); 
registerReceiver(myReceiver, filter);
```
另外需要加上权限控制：
```  
<uses-permission android:name="android.permission.READ_PHONE_STATE"> 
</uses-permission>
```
具体步骤实现如下：
第一步：
先画一个按钮，然后通过点击这个按钮退出程序，这里只是为了让该程序安装到手机或者模拟器里
/BroadcastReceiverDemo/res/layout/main.xml
```  
<?xml version="1.0" encoding="utf-8"?> 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
	android:orientation="vertical"
	android:layout_width="fill_parent"
	android:layout_height="fill_parent" >
<Button android:id="@+id/button" 
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:text="启动BroadcastReceiver"/>
</LinearLayout>
```
第二步：
对main.xml里的按钮进行事件监听，单击后退出程序
/BroadcastReceiverDemo/src/com/broadcast/activity/MainActivity.java
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class MainActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button button = (Button) findViewById(R.id.button);
		View.OnClickListener listener = new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				System.exit(0);
			}
		};
		button.setOnClickListener(listener);
	}
}
```
第三步：
定义一个广播接收者
/BroadcastReceiverDemo/src/com/broadcast/activity/MyReceiver.java
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
public class MyReceiver extends BroadcastReceiver {
	// 这里intent是来自于拨打电话，而intentTemp是用来跳转到MainActivity界面里
	@Override
	public void onReceive(Context context, Intent intent) {
		Intent intentTemp = new Intent(context, MainActivity.class);
		// 必须为这个intent设置一个Flag
		intentTemp.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
		context.startActivity(intentTemp);
	}
}
```
第四步：注册MyReceiver，并开启相关权限
/BroadcastReceiverDemo/AndroidManifest.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.broadcast.activity"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <receiver android:name=".MyReceiver" >
            <intent-filter>
                <!-- 得到电话状态广播，可以得到拨打电话 -->
                <action android:name="android.intent.action.PHONE_STATE" />
            </intent-filter>
        </receiver>
    </application>
    <uses-sdk android:minSdkVersion="8" />
    <!-- 设置该程序能够读取到电话状态 -->
    <uses-permission android:name="android.permission.READ_PHONE_STATE" >
    </uses-permission>
</manifest> 
```
操作步骤：
运行该程序，然后点击按钮后会关闭该程序（表示已经安装到系统），然后拨打电话，接下来该程序界面又会出现。