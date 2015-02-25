接触Android也有半年了，小作品也发布了几个，但是其中都没有用到Servcie，在这一点觉得自己还是有所不足的。目前正在做的这个应用要使用到Service，所以就当是补补课了。
应用的目标很简单，用户设定一个时间，到时后停止音乐的播放。所以我们需要使用Service来保持在Activity结束后继续维持计时。当用户设定某一时间后启动一个Service，之后所有操作由Service驱动，Activity界面就可以关闭了，在Service中我们要使用 AlarmService来实现计时，当时间到时AlarmManager会发送一个广播，你需要一个BroadcastReceiver来处理这个广播完成时间到时时要完成的操作。
如果说正在看这篇文章的你还不知道什么是Service或BroadcastReceiver的话，您需要自己另找资料学习一下了，我只能简单说 Service是一个后台的应用程序，它没有显示的界面所以也就不能与用户交互，但是它还是能够通信的。Service有两种启动的方式一个使用 Context.startService()启动，另一个则是使用Context.bindService()来启动，两者存在这区别。而BroadCastReceiver就是一个收音机，这个BroadCastReceiver会响应一个有特定标识的消息。我也只能简单的说这一点，更多的内容你可以自己在Google上搜索一些关于Service和BroadcastReceiver的资料吧。
首先是要做的是一个Service，你需要继承Service类并实现它的onCreate(),onStart(),onDestroy(),onBind()方法，其中onBind()方法是必须实现的。
实例代码如下
```  
import java.util.Calendar;
//......
import com.shinestudio.sleepMusic.AlarmReceiver;
import com.shinestudio.sleepMusic.StartActivity;
public class SleepMusicService extends Service {
	private static String TAG = "sleepMusicService";
	private static SleepMusicService sms = null;
	private static int NOTIFICATION_ID = 0x1209;
	private String settingTime;
	public static Service getService() {
		return sms;
	}
	/*
	 [Tags]* 这里有个小方法有必要说一下，在Service或Activity中我们可以写一个静态的方法来保留自己的实体。这样在其他的地方就可以获取到了。
	 [Tags]* private static SleepMusicService sms = null; 用来存储自己的实体 在onCreate()中 使用sms
	 [Tags]* = this;来存储实体 编写一个静态的getService()来返回实体就行了。
	 [Tags]*/
	@Override
	public void onCreate() {
		super.onCreate();
		sms = this;
	}
	@Override
	public void onStart(Intent intent, int startId) {
		super.onStart(intent, startId);
		Log.d(TAG, "Service onStart");
		// 获取AlarmManager
		AlarmManager am = (AlarmManager) getSystemService(Service.ALARM_SERVICE);
		// 获取当前的时间
		Calendar c = Calendar.getInstance();
		c.setTimeInMillis(System.currentTimeMillis());
		// 只对秒 做修改
		// c.set(Calendar.HOUR_OF_DAY, hourOfDay);
		// c.set(Calendar.MINUTE, c.get(Calendar.MINUTE));
		c.set(Calendar.SECOND, c.get(Calendar.SECOND) + 5); // 定时5秒
		// c.set(Calendar.MILLISECOND, 0);
		// 设置消息的响应
		Intent ii = new Intent(this, AlarmReceiver.class);
		PendingIntent pii = PendingIntent.getBroadcast(this, 0, ii, 0);
		am.set(AlarmManager.RTC_WAKEUP, c.getTimeInMillis(), pii);
		// 使用Toast提示用户
		Toast.makeText(this, "AlarmSet Finish", Toast.LENGTH_SHORT).show();
	}
	@Override
	public void onDestroy() {
		AlarmManager am = (AlarmManager) getSystemService(Service.ALARM_SERVICE);
		Intent i = new Intent(this, StartActivity.class);
		PendingIntent pi = PendingIntent.getActivity(this, 0, i, 0);
		am.cancel(pi);
		super.onDestroy();
	}
	@Override
	public IBinder onBind(Intent intent) {
		return null;
	}
}
```
完成了Service，之后就是写一个Activity来启动这个Service，其中为了保证在应用退出后Servcie继续运行，所以要使用startService()来启动Service。一般关于Service的资料上都是有的。
```  
import java.util.Iterator;
import java.util.List;
import com.shinestudio.sleepMusic.service.ISleepMusicService;
import com.shinestudio.sleepMusic.service.SleepMusicService;
import com.shinestudio.sleepMusic.unit.TimerPickerUIStruct;
public class StartActivity extends Activity {
	private static String TAG = "sleepMusic - StartActivity";
	private ListView timerlist;
	private TimerPicker timerPicker;
	private TimerPickerUIStruct tpui;
	private Button cancelButton;
	private Button restartButton;
	// 当点击开始按钮
	private OnClickListener startButtonClickListener = new OnClickListener() {
		@Override
		public void onClick(View v) {
			Intent isleepMusicService = new Intent(StartActivity.this,
					SleepMusicService.class);
			/*
			 [Tags]* 在Service启动之前可以使用Intent来传递参数给Service ，方法如下 目前的代码只是演示，与功能无关
			 [Tags]*/
			Bundle setting = new Bundle();
			setting.putString("TIME_SETTING", "5s");
			// 在Service中使用“TIME_SETTING”这个标签就可以从Intent取出5s 这个字符串了
			isleepMusicService.putExtras(setting);
			startService(isleepMusicService);
		}
	};
	// 点击取消按钮
	private OnClickListener cancelButtonClickListener = new OnClickListener() {
		@Override
		public void onClick(View v) {
			Intent sleepMusicService = new Intent(StartActivity.this,
					SleepMusicService.class);
			// 停止服务
			stopService(sleepMusicService);
		}
	};
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// ...... 代码省略
		// 获取Layout中的按钮
		startButton = (Button) findViewById(R.id.b_start);
		cancelButton = (Button) findViewById(R.id.b_cancel);
		startButton.setOnClickListener(startButtonClickListener);
		cancelButton.setOnClickListener(cancelButtonClickListener);
		restartButton = (Button) findViewById(R.id.b_restart);
		restartButton.setOnClickListener(restartButtonClickListener);
	}
}
```
完成了这个Activity之后，就容易的多了，剩下的就是BroadcastReceiver了，新建一个类继承BroadcastReceiver
并且实现onReceive()方法
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;
public class AlarmReceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		Toast.makeText(context, "时间到", Toast.LENGTH_SHORT).show();
	}
}
```
当Service中的AlarmManager完成计时后将广播消息给AlarmReceiver，这样就会显示Toast给用户了。
在AndroidManifest.xml中添加activity、service、和receiver的设置
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.shinestudio.sleepMusic"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".StartActivity"
            android:label="@string/app_name"
            android:theme="@android:style/Theme.Black.NoTitleBar" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <service android:name=".service.SleepMusicService" >
        </service>
        <receiver android:name="AlarmReceiver" >
        </receiver>
    </application>
</manifest>
```
附加提供的一点点代码片
打开HTC HERO 自带的HTC Music Player
```  
public void openMusicPlayer() {
	Intent musicPlayer = new Intent();
	musicPlayer.setAction(Intent.ACTION_MAIN);
	musicPlayer.setPackage("com.htc.music");
	StartActivity.this.startActivity(musicPlayer);
}
// 获得所有运行的service
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
		serviceInfo.append("\nprocess: ").append(si.process);
		serviceInfo.append("\nservice: ").append(si.service);
		serviceInfo.append("\ncrashCount: ").append(si.crashCount);
		serviceInfo.append("\nclientCount: ").append(si.clientCount);
		serviceInfo.append("\n\n");
	}
	return serviceInfo.toString();
}
```
在看完这堆代码之后，请您看看这个，这些是Android所提供给用户的的一下系统的Service
像NotificationManager Vebrator AlarmManager 都是比较常用的。
```  
WINDOW_SERVICE                      WindowManager                    管理打开的窗口程序
LAYOUT_INFLATER_SERVICE             LayoutInflater                   取得xml里定义的view
ACTIVITY_SERVICE                    ActivityManager                  管理应用程序的系统状态
POWER_SERVICE                       PowerManger                      电源的服务
ALARM_SERVICE                       AlarmManager                     闹钟的服务
NOTIFICATION_SERVICE                NotificationManager              状态栏的服务
KEYGUARD_SERVICE                    KeyguardManager                  键盘锁的服务
LOCATION_SERVICE                    LocationManager                  位置的服务，如GPS
SEARCH_SERVICE                      SearchManager                    搜索的服务
VEBRATOR_SERVICE                    Vebrator                         手机震动的服务
CONNECTIVITY_SERVICE                Connectivity                     网络连接的服务
WIFI_SERVICE                        WifiManager                      Wi-Fi服务
TELEPHONY_SERVICE                   TeleponyManager                  电话服务
```