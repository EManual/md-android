WIFI是无线通信协议，可以允许手机直接连接到无线网络。在现在3G资费还比较贵的情况下，WIFI对于手机来说是很重要的，我们可以很方便的下载软件，音乐等资源。Android手机必须要有WIFI网卡才能支持WIFI。Android应用程序有时候需要对WIFI网卡进行操作，从而操作WIFI网络。 
WIFI网卡有一些状态，由一系列的整形常量来表示。
```  
常量名	常量值	网卡状态
WIFI_STATE_DISABLED 	1	WIFI网卡不可用
WIFI_STATE_DISABLING	0	WIFI正在关闭
WIFI_STATE_ENABLED	3	WIFI网卡可用
WIFI_STATE_ENABLING	2	WIFI网卡正在打开
WIFI_STATE_UNKNOWN	4	未知网卡状态
```
在应用程序中操作WIFI网卡一定的权限。 
WIFI 的主要操作权限有四个： 
```  
CHANGE_NETWORK_STATE ：允许修改网络状态的权限。 
CHANGE_WIFI_STATE ：允许修改 WIFI 状态的权限。 
ACCESS_NETWORK_STATE ：允许访问网络状态的权限。 
ACCESS_WIFI_STATE ：允许访问 WIFI 状态的权限。 
```
在AndroidManifest.xml文件中添加权限。 
```  
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"></uses-permission>  
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"></uses-permission>  
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>  
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"></uses-permission> 
```  
改变WIFI网卡的状态 
对WIFI网卡进行操作需要通过WifiManager对象来进行，获取该对象的方法如下： 
```  
WifiManager wifiManger=(WifiManger)Context.getSystemService(Service.WIFI-SERVICE); 
打开WIFI网卡 
wifiManager.setWifiEnabled(true); 
关闭WIFI网卡 
wifiManager.setWifiEnabled(false); 
获取网卡当前的状态 
wifiManager.getWifiState();
```
示例：新建一个一个Android应用程序，在main.xml中添加三个按钮，点击这三个按钮分别可以打开WIFI网卡，关闭WIFI网卡，检查网卡  的当前状态。需要说明的是由于Android模拟器不支持WIFI和蓝牙所以程序执行时返回的网卡状态都是WIFI_STATE_UNKNOWN：网卡未知的状态。此程序需要在真机上进行调试才会显示正确的运行结果。这里主要是为了说明程序如何编写。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <Button
        android:id="@+id/startButton"
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:text="打开WIFI网卡" />
    <Button
        android:id="@+id/stopButton
        android:layout_width="300dp
        android:layout_height="wrap_content
        android:text="关闭WIFI网卡" />
    <Button
        android:id="@+id/checkButton"
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:text="检查WIFI网卡状态" />
</LinearLayout>
```
代码如下：
```     
import android.app.Activity;
import android.content.Context;
import android.net.wifi.WifiManager;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.Toast;
public class Android_Wifi extends Activity {
	private Button startButton = null;
	private Button stopButton = null;
	private Button checkButton = null;
	WifiManager wifiManager = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		startButton = (Button) findViewById(R.id.startButton);
		stopButton = (Button) findViewById(R.id.stopButton);
		checkButton = (Button) findViewById(R.id.checkButton);
		startButton.setOnClickListener(new startButtonListener());
		stopButton.setOnClickListener(new stopButtonListener());
		checkButton.setOnClickListener(new checkButtonListener());
	}
	class startButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			wifiManager = (WifiManager) Android_Wifi.this
					.getSystemService(Context.WIFI_SERVICE);
			wifiManager.setWifiEnabled(true);
			System.out.println("wifi state --->" + wifiManager.getWifiState());
			Toast.makeText(Android_Wifi.this,
					"当前网卡状态为：" + wifiManager.getWifiState(), Toast.LENGTH_SHORT)
					.show();
		}
	}
	class stopButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			wifiManager = (WifiManager) Android_Wifi.this
					.getSystemService(Context.WIFI_SERVICE);
			wifiManager.setWifiEnabled(false);
			System.out.println("wifi state --->" + wifiManager.getWifiState());
			Toast.makeText(Android_Wifi.this,
					"当前网卡状态为：" + wifiManager.getWifiState(), Toast.LENGTH_SHORT)
					.show();
		}
	}
	class checkButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			wifiManager = (WifiManager) Android_Wifi.this
					.getSystemService(Context.WIFI_SERVICE);
			System.out.println("wifi state --->" + wifiManager.getWifiState());
			Toast.makeText(Android_Wifi.this,
					"当前网卡状态为：" + wifiManager.getWifiState(), Toast.LENGTH_SHORT)
					.show();
		}
	}
}
XML如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="idea.org"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="11" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".Android_Wifi"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
</manifest>
```
依次点击“打开WIFI网卡”，“关闭WIFI网卡”，“检查WIFI网卡状态”三个Button按钮，控制台输出一下内容。