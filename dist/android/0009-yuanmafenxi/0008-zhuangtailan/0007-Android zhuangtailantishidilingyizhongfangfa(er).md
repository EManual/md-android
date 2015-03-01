#### NotificationManager与Notification对象的应用
src/irdc.ex05_08/EX05_08_1.java
当用户在单击Nitification列表中，MSN登录状态的Notification时，会启动这个程序，程序会发出一个Toast，并告知用户"这是模拟MSN切换登录状态的程序"。
```  
/* 当user单击Notification留言条时，会运行的Activity */
public class EX05_08_1 extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		/* 发出Toast */
		Toast.makeText(EX05_08_1.this, "这是模拟MSN切换登录状态的程序", Toast.LENGTH_SHORT)
				.show();
		finish();
	}
}
```
AndroidManifest.xml
本应用范例程序里有两个Activity，一个是LAUNCHER启动时运行，另一个是用户单击Nitification列表中的MSN登录状态时，显示Toast的Activity。如以下的Manifest所述。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="irdc.ex05_08"
    android:versionCode="1"
    android:versionName="1.0.0" >
    <application
        android:icon="@drawable/icon
        android:label="@string/app_name" >
        <activity
            android:name=".EX05_08"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".EX05_08_1"
            android:label="@string/app_name" >
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="7" />
</manifest>
```
本范例的学习重点在于如何运用NotificationManager来发出Notification，这在目前的手机上已经被广泛的应用，比如说，显示新短信提示、显示未接来电提示、显示系统错误信息提示等，在开发Android应用程序时可以好好利用这个功能。
发出Notification时，有三个地方可以显示文字，第一个是当Notification被添加到状态栏时，跟着ICON跑出的文字，在程序中以myNoti.tickerText=text来设置；其余两个地方则是在Notification列表中所显示的信息标题与信息内容，是在调用setLatestEventInfo()这个方法时所设置的，那么，如果希望在状态栏中不要显示文字，只要让ICON做变化，要怎么做呢？很简单，只要不设置Notification的tickerText，就只有ICON会改变，没有文字提示；同理，如果希望在列表的地方没有标题或内容，只要传入空白字符串就可以了。
在范例中，在线状态改变的同时，会发出系统默认的声音，主要是因为以下这一行程序：
```  
myNoti.defaults=Notification.DEFAULT_SOUND;
```
Notification的defaults属性列表
```  
Notification.DEFAULT_SOUND
发出系统默认的声音
Notification.DEFAULT_LIGHTS
屏幕发亮
Notification.DEFAULT_VIBRATE
手机震动
Notification.DEFAULT_ALL
包含以上三种的动作
```
通过上述几种方式，可以设置在发出Notification时，要同时运行的动作，甚至还可以通过设置Notification的sound属性（Url对象）来指定要播放的声音。
程序中用NotificationManager.notify(id,Notification)来发出信息，其中传入的第一个参数id就是Notification的id。以本范例而言，由于每次更改在线状态时，发出的Notification的id皆为0，因此当新的Notification被发出时，会替换掉旧的Notification，造成不断变换状态栏上的ICON的效果，若发出的Notification的id不同，状态栏上就会显示不同的ICON。