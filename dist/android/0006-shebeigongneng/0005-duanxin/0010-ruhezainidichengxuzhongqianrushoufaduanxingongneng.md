注1：本文原文为英文版本的教程，我只在其基础上加以修改和汉化。
注2：本文作为初学者入门，简要的运用了几个基本的功能。
在这篇文章里将介绍如何在你的android应用中嵌入收发短信的功能。我们不需要拿真机来测试，只需要拿android仿真器就能调试和测试收发短信的功能（当然有条件可以在真机上试试，不过发短信要收钱的哦^_^）。
本文描述的步骤是基于eclipse开发的，阅读本文的前提是已经搭建好java环境，android sdk已经下载了，eclipse上已把Android开发的插件安装好了。
#### 一、发短信功能
1、创建android项目
首先打开Eclipse，文件->新建->项目，创建一个新的Android project。
2、声明权限
Android是通过基于权限声明的策略来请求系统功能权限的，我们这里所说的收发短信的功能就是需要声明后才能使用的。下面我们来声明这个权限。
Android的权限声明是在AndroidManifest.xml文件中声明的。我们找到AndroidManifest.xml文件，在里面增加两个permissions：SEND_SMS和RECEIVE_SMS（见uses-permission部分）：
```  
<?xml version ="1.0" encoding ="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="net.learn2develop.SMSMessaging"
    android:versionCode="1"
    android:versionName="1.0.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".SMS"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.SEND_SMS" >
    </uses-permission>
    <uses-permission android:name="android.permission.RECEIVE_SMS" >
    </uses-permission>
</manifest>
```
3、设计一个粗略的界面
下面我们来编写程序的界面UI，在res/layout文件夹中找到main.xml文件，增加如下代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="联系人号码" />
    <EditText
        android:id="@+id/txtPhoneNo"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="信息内容" />
    <EditText
        android:id="@+id/txtMessage"
        android:layout_width="fill_parent"
        android:layout_height="150px"
        android:gravity="top" />
    <Button
        android:id="@+id/btnSendSMS"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="直接发送" />
</LinearLayout>
```
上面的代码包括两个输入框和一个按钮。
4、实现发短信方法
下面我们来实现发短信的方法。我们在主程序activity里，做如下三样事情：
1）绑定Button的onClick监听。
2）判断输入了要发短信的手机号码和短信内容。
3）写一个sendSMS()来实现发短信。
```  
import android.app.Activity;
import android.os.Bundle;
import android.app.PendingIntent;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.telephony.SmsManager;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
public class Lesson2devActivity extends Activity {
	Button btnSendSMS;
	EditText txtPhoneNo;
	EditText txtMessage;
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		btnSendSMS = (Button) findViewById(R.id.btnSendSMS);
		txtPhoneNo = (EditText) findViewById(R.id.txtPhoneNo);
		txtMessage = (EditText) findViewById(R.id.txtMessage);
		// 创建listener
		btnSendSMS.setOnClickListener(new View.OnClickListener() {
			public void onClick(View v) {
				String phoneNo = txtPhoneNo.getText().toString();
				String message = txtMessage.getText().toString();
				if (phoneNo.length() > 0 && message.length() > 0)
					sendSMS1(phoneNo, message);
				else
					Toast.makeText(getBaseContext(),
							"Please enter both phone number and message.",
							Toast.LENGTH_SHORT).show();
			}
		});
	}
}
```
其中sendSMS()方法代码如下：
```  
// ---sends an SMS message to another device---
private void sendSMS(String phoneNumber, String message) {
	PendingIntent pi = PendingIntent.getActivity(this, 0, new Intent(this,
			SMS.class), 0);
	SmsManager sms = SmsManager.getDefault();
	sms.sendTextMessage(phoneNumber, null, message, pi, null);
}
```
我们用SmsManager这个类来发送短信。你不需要直接对这个类进行实例化，只需要调用其getDefault()静态方法来获得一个SmsManager对象。然后通过其sendTextMessage()方法来发送短信。该方法调用参数为PendingIntent类型，PendingIntent这个类用于处理即将发生的事件而不是立即执行，也就是不一定由本对象来触发执行。
下面我们对这个sendSMS方法进行完善：
如果你需要监控这个发送短信的状态，你可以使用两个PendingIntent对象，通过两个BroadcastReceiver来实现：
```  
// ---sends an SMS message to another device---
private void sendSMS(String phoneNumber, String message) {
	String SENT = "SMS_SENT";
	String DELIVERED = "SMS_DELIVERED";
	PendingIntent sentPI = PendingIntent.getBroadcast(this, 0, new Intent(
			SENT), 0);
	PendingIntent deliveredPI = PendingIntent.getBroadcast(this, 0,
			new Intent(DELIVERED), 0);
	// ---when the SMS has been sent---
	registerReceiver(new BroadcastReceiver() {
		@Override
		public void onReceive(Context arg0, Intent arg1) {
			switch (getResultCode()) {
			case Activity.RESULT_OK:
				Toast.makeText(getBaseContext(), "SMS sent",
						Toast.LENGTH_SHORT).show();
				break;
			case SmsManager.RESULT_ERROR_GENERIC_FAILURE:
				Toast.makeText(getBaseContext(), "Generic failure",
						Toast.LENGTH_SHORT).show();
				break;
			case SmsManager.RESULT_ERROR_NO_SERVICE:
				Toast.makeText(getBaseContext(), "No service",
						Toast.LENGTH_SHORT).show();
				break;
			case SmsManager.RESULT_ERROR_NULL_PDU:
				Toast.makeText(getBaseContext(), "Null PDU",
						Toast.LENGTH_SHORT).show();
				break;
			case SmsManager.RESULT_ERROR_RADIO_OFF:
				Toast.makeText(getBaseContext(), "Radio off",
						Toast.LENGTH_SHORT).show();
				break;
			}
		}
	}, new IntentFilter(SENT));
	// ---when the SMS has been delivered---
	registerReceiver(new BroadcastReceiver() {
		@Override
		public void onReceive(Context arg0, Intent arg1) {
			switch (getResultCode()) {
			case Activity.RESULT_OK:
				Toast.makeText(getBaseContext(), "SMS delivered",
						Toast.LENGTH_SHORT).show();
				break;
			case Activity.RESULT_CANCELED:
				Toast.makeText(getBaseContext(), "SMS not delivered",
						Toast.LENGTH_SHORT).show();
				break;
			}
		}
	}, new IntentFilter(DELIVERED));
	SmsManager sms = SmsManager.getDefault();
	sms.sendTextMessage(phoneNumber, null, message, sentPI, deliveredPI);
}
```
上述代码用一个sentPI的PendingIntent对象来监控短信发送的状态，当一个短信发出去之后，第一个BroadcastReceiver的onReceive事件就会被激活，这个Receiver就会监听事件的状态。第二个deliveredPI用来监控短信收到的状态，当短信被收到后，第二个BroadcastReceiver的onReceive事件会激活。
5、功能测试
现在你可以在Eclipse上按F11来进行程序的仿真器上测试。
按下F11后，会弹出仿真器并打开你的程序。我们在输入框输入相应的信息，点击发送，会显示发送成功。
我们会发现，在仿真器能收到”SMS sent”的信息，但是收不到”SMS delivered”的信息。这是因为后者的监听只会在真机上出现，仿真器上不会出现。
在这个时候，我们可以再打开一个仿真器来测试两个仿真器之间互发短信。
如何打开一个仿真器？在路径\android-sdk-windows\tools\emulator.exe，打开这个emulator即可，仿真器的号码是窗口抬头，一般是5556～5558之类的。大家来互发试试。
6、另外一个发短信的方法
另外一种方法：调用android系统自身的SMS功能。代码如下：
```  
private void sendSMS(String phoneNumber, String message) {
	Intent sendIntent = new Intent(Intent.ACTION_VIEW);
	sendIntent.putExtra("sms_body", message);
	sendIntent.putExtra("address", phoneNumber);
	sendIntent.setType("vnd.android-dir/mms-sms");
	startActivity(sendIntent);
}
```
这里，我们通过intent来调用android系统自身的SMS功能，主要是通过设置intent的类型和输入参数即可。这个程序还可以作为短信预览功能。
#### 二、收短信功能
除了发短信，我们还要做收短信功能。这里可以通过BroadcastReceiver对象来实现收短信。
1、注册receiver
首先我们在AndroidManifest.xml中增加<receiver>字段：
```  
<?xml version ="1.0" encoding ="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="net.learn2develop.SMSMessaging"
    android:versionCode="1"
    android:versionName="1.0.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".SMS"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <receiver android:name=".SmsReceiver" >
            <intent-filter>
                <action android:name="android.provider.Telephony.SMS_RECEIVED" />
            </intent-filter>
        </receiver>
    </application>
    <uses-permission android:name="android.permission.SEND_SMS" >
    </uses-permission>
    <uses-permission android:name="android.permission.RECEIVE_SMS" >
    </uses-permission>
</manifest>
```
2、新增SmsReceiver.java
在原有项目中增加一个类文件SmsReceiver.java。这个类继承BroadcastReceiver并重写onReceiver()方法。
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.telephony.SmsMessage;
import android.widget.Toast;
public class SmsReceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		// ---get the SMS message passed in---
		Bundle bundle = intent.getExtras();
		SmsMessage[] msgs = null;
		String str = "";
		if (bundle != null) {
			// ---retrieve the SMS message received---
			Object[] pdus = (Object[]) bundle.get("pdus");
			msgs = new SmsMessage[pdus.length];
			for (int i = 0; i < msgs.length; i++) {
				msgs[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
				str += "SMS from " + msgs[i].getOriginatingAddress();
				str += " :";
				str += msgs[i].getMessageBody().toString();
				str += "/n ";
			}
			// ---display the new SMS message---
			Toast.makeText(context, str, Toast.LENGTH_SHORT).show();
		}
	}
}
```
当有短信到达的时候，SmsReceiver类的onCreate()就会被激活。
这是如何做到的呢？我们在前面做了一个注册Receiver的步骤，其实BroadcastReceiver既可以在资源AndroidManifest.xml中注册，也可以在代码中通过Context.registerReceiver()进行注册。只要是注册了，当该事件来临的时候，即时程序没有启动，系统也在需要的时候会自动启动此应用程序。也就能够响应相应事件了。
在这个程序里，是在AndroidManifest.xml中增加的<receiver>来关联的（android:name的命名要跟类命名一致）。
在android系统上，当收到了一条SMS短信，它其实是一个intent对象，经由Bundle附着在一个intent对象里（就是onReceive()方法的第二个参数）。这个短信会保存在一个以PDU格式为对象的数组中。为了获取短信息的内容，我们使用了SmsMessage类里面的createFromPdu()这个静态方法。
最后我们把短信内容呈现出来。