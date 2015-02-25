一、开发环境
Elispse，JDK1.6，Aadroid 2.1
二、开发中使用到的重点技术点：
距离感应(SENSOR_SERVICE ),音讯管理(AUDIO_SERVICE ),
电话状态监听 (TELEPHONY_SERVICE)，
java反射启动自动接听,开机自动启动Service，
监听来电，在Service 中启动Activity 并传递参数
三、主要开发流程：
1. 在前三步中我们看到有一个公共的辅助类CommonHelper
```  
import android.content.Context;
import android.content.Intent;
public class CommonHelper { // 保存电话状态
	public static int phoneState = 0; // 保存音讯管理对象
	public static MyAudioManager mam = null; // 保存去点电话号码
	public static String outGoingPhoneNumber = "";
	// 启动一个新的Activity
	public static void StartCustomerInfoActivity(Context context, String telNo) {
		// 第一个参数
		// 启动新的Acitivity的Context;
		// 第二个参数
		// 启动的Acitivity的类
		Intent intent = new Intent(context, CustomerInfo.class); // 在Service中启动一个Activity并需添加此Flag
		intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK); // 启动新的Activity时传递的参数
		intent.putExtra("TelNo", telNo); // 启动新的Activity
		context.startActivity(intent);
	}
}
```
其实将这个类放到第一步是不合适的，因为这个公共类实在我不断写码过程中完善的，并不是一开始就创建的。
2.首先我们建立我们一个音讯管理的类，用于管理当来电或者去点时扩音器的开关。
```  
import android.content.Context;
import android.media.AudioManager;
public class MyAudioManager {
	private AudioManager audioManager;
	private int currVolume = 0;
	public MyAudioManager(Object object, Context mc) {
		// 音频管理对象由外部调用时传入（http://www.my400800.cn ）
		// this.audioManager=(AudioManager)object; this.context=mc;
		// //设置音讯模式为对外输出
		this.audioManager.setMode(AudioManager.ROUTE_SPEAKER); // 取得当前的音量
		currVolume = audioManager
				.getStreamVolume(AudioManager.STREAM_VOICE_CALL);
	}
	// 打开扬声器
	public void OpenSpeaker() { // 设置为true，打开扬声器
		audioManager.setSpeakerphoneOn(true); // 设置打开扬声器的音量为最大
		audioManager
				.setStreamVolume(AudioManager.STREAM_VOICE_CALL, audioManager
						.getStreamMaxVolume(AudioManager.STREAM_VOICE_CALL),
						AudioManager.STREAM_VOICE_CALL);
		// Toast.makeText(context,"揚聲器已經打開",Toast.LENGTH_SHORT).show();
	}
	// 关闭扬声器
	public void CloseSpeaker() { // 设置为false，关闭已经打开的扬声器
		audioManager.setSpeakerphoneOn(false); // 恢复为正常音量
		audioManager.setStreamVolume(AudioManager.STREAM_VOICE_CALL,
				currVolume, AudioManager.STREAM_VOICE_CALL);
		// Toast.makeText(context,"揚聲器已經關閉",Toast.LENGTH_SHORT).show();
	}
}
```
3.建立一个监听电话状态的类
```  
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import com.android.internal.*;
import com.android.internal.telephony.ITelephony;
import android.content.Context;
import android.os.RemoteException;
import android.telephony.PhoneStateListener;
import android.telephony.TelephonyManager;
import android.widget.Toast;
public class SpeakMananger {
	private TelephonyManager teleManager;
	private Context context;
	public SpeakMananger(Object object, Context p_context) { // 转换从外部传入的Object为TelephonyManager对象
		this.teleManager = (TelephonyManager) object;
		this.context = p_context;
	}
	// 注册电话状态监听器
	public void RegisterListener() {
		teleManager.listen(new MyPhoneStateListener(),
				PhoneStateListener.LISTEN_CALL_STATE);
	}
	class MyPhoneStateListener extends PhoneStateListener {
		@Override
		public void onCallStateChanged(int state, String incomingNumber) {
			super.onCallStateChanged(state, incomingNumber);
			switch (state) {
			// 空闲 没有电话打入或者打出 处于挂机空闲状态
			case TelephonyManager.CALL_STATE_IDLE:
				// Toast.makeText(context,"空閒",Toast.LENGTH_SHORT).show();
				break;
			// 摘机 有电话打出或者打入 按接听接或者其它操作拨打或接听来电
			case TelephonyManager.CALL_STATE_OFFHOOK:
				// Toast.makeText(context,"摘機",Toast.LENGTH_SHORT).show();

				// 去電 如果是去電則incomingNumber為null或""
				// //【因为此处无法监听去电并且无法去电的电话号码，所以当去电时此处的打入电话号码为null或者""】
				if (incomingNumber == null || incomingNumber.length() <= 0) { // 打开扬声器
					CommonHelper.mam.OpenSpeaker(); // 根据公共类中保存的去电电话号码启动一个新的Activity
					CommonHelper.StartCustomerInfoActivity(context,
							CommonHelper.outGoingPhoneNumber);
				}
				// else //來電 本人是需要在来电振铃的时候就打开扩音器和启动一个新的Acitivity，所以此处被注释掉
				// CommonHelper.StartCustomerInfoActivity(context,incomingNumber);
				// break;
				// 振铃 在此处我需要在振铃的时候自动接听电话并且打开扬声器和新启动一个Activity
			case TelephonyManager.CALL_STATE_RINGING:
				// Toast.makeText(context,"振鈴",Toast.LENGTH_SHORT).show();
				try {
					/* 以下两种方法都可以使用 都能完成自动接听 但是第一种的异常信息处理的比较完善 */
					// StartCall();
					answerPhoneAidl(); // 新启动一个Activity
					CommonHelper.StartCustomerInfoActivity(context,
							incomingNumber);
				} catch (Exception e) {
					Toast.makeText(context, "自动接听发生异常：" + e.getMessage(),
							Toast.LENGTH_LONG).show();
				}
				break;
			} // 保存当前电话状态
			CommonHelper.phoneState = state;
		}
	}
	// 此自动接听代码来自官方开源Demo http://code.google.com/p/auto-answer/source/detail?r=17
	private void answerPhoneAidl() throws Exception {
		Class c = Class.forName(teleManager.getClass().getName());
		Method m = c.getDeclaredMethod("getITelephony", (Class[]) null);
		m.setAccessible(true);
		ITelephony telephonyService;
		telephonyService = (ITelephony) m.invoke(teleManager, (Object[]) null);
		// Silence the ringer and answer the call!
		telephonyService.silenceRinger();
		telephonyService.answerRingingCall();
	}
	[Tags]/**
	 [Tags]* 利用JAVA反射机制调用ITelephony的answerRingingCall()开始通话。
	 [Tags]*/
	private void StartCall() {
		// 初始化iTelephony
		Class<TelephonyManager> c = TelephonyManager.class;
		Method getITelephonyMethod = null;
		try {
			// 获取所有public/private/protected/默认
			// 方法的函数，如果只需要获取public方法，则可以调用getMethod.
			getITelephonyMethod = c.getDeclaredMethod("getITelephony",
					(Class[]) null);
			// 将要执行的方法对象设置是否进行访问检查，也就是说对于public/private/protected/默认
			// 我们是否能够访问。值为 true 则指示反射的对象在使用时应该取消 Java 语言访问检查。值为 false
			// 则指示反射的对象应该实施 Java 语言访问检查。
			getITelephonyMethod.setAccessible(true);
		} catch (SecurityException e) {
			Toast.makeText(context, "安全异常：" + e.getMessage(),
					Toast.LENGTH_SHORT).show();
		} catch (NoSuchMethodException e) {
			Toast.makeText(context, "未找到方法：" + e.getMessage(),
					Toast.LENGTH_SHORT).show();
		}
		try {
			ITelephony iTelephony = (ITelephony) getITelephonyMethod.invoke(
					teleManager, (Object[]) null); // 停止响铃
			iTelephony.silenceRinger(); // 接听来电
			iTelephony.answerRingingCall();
		} catch (IllegalArgumentException e) {
			Toast.makeText(context, "参数异常：" + e.getMessage(),
					Toast.LENGTH_SHORT).show();
		} catch (IllegalAccessException e) {
			Toast.makeText(context, "进入权限异常：" + e.getMessage(),
					Toast.LENGTH_SHORT).show();
		} catch (InvocationTargetException e) {
			Toast.makeText(context, "目标异常：" + e.getMessage(),
					Toast.LENGTH_SHORT).show();
		} catch (RemoteException e) {
			Toast.makeText(context, "Remote异常：" + e.getMessage(),
					Toast.LENGTH_SHORT).show();
		}
	}
}
```
4.建立一个用于监听距离感应的类
```  
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.telephony.TelephonyManager;
public class ProximitySensor {
	private SensorManager sensorManager;
	private Sensor sensor;
	private SensorEventListener listener;
	public ProximitySensor(Object object) { // 获得外部传入的SensorManager对象
		this.sensorManager = (SensorManager) object; // 取得距离感应类型的感应器
		this.sensor = sensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);
		listener = new SensorEventListener() {
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
			}
			@Override
			public void onSensorChanged(SensorEvent event) {
				if (event.values[0] == 1.0) {
					switch (CommonHelper.phoneState) {
					// 空闲 离开耳边并且电话空闲的状态，关闭扬声器
					case TelephonyManager.CALL_STATE_IDLE:
						CommonHelper.mam.CloseSpeaker();
						break;
					// 摘机 离开耳边并且电话状态为摘机状态，打开扬声器
					case TelephonyManager.CALL_STATE_OFFHOOK:
						CommonHelper.mam.OpenSpeaker();
						break;
					}
				} else { // 耳边接听电话关闭扬声器
					CommonHelper.mam.CloseSpeaker();
				}
			}
		};
	}
	// 注册监听器
	public void RegisterListener() {
		this.sensorManager.registerListener(listener, sensor,
				SensorManager.SENSOR_DELAY_FASTEST);
	}
	// 移除监听器
	public void UnRegisterListener() {
		this.sensorManager.unregisterListener(listener);
	}
}
```
5.现在我们建立我们的Service
```  
import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
public class PhoneCallStateService extends Service {
	@Overide
	public IBinder onBind(Intent intent) {
		return null;
	}
	@Override
	public void onCreate() {
		super.onCreate();
	}
	/* 在Service启动的时候，实例化我们刚才建立的所有类 */
	@Override
	public void onStart(Intent intent, int startId) {
		super.onStart(intent, startId);
		// 音频管理服务
		MyAudioManager am = new MyAudioManager(
				this.getSystemService(AUDIO_SERVICE), this); // 将音讯管理对象保存到公共类中
		CommonHelper.mam = am;
		// 电话-监听-服务【是否有来电、去电、是否空闲】
		SpeakMananger sm = new SpeakMananger(
				this.getSystemService(TELEPHONY_SERVICE), this);
		sm.RegisterListener();
		// 距离感应
		ProximitySensor ps = new ProximitySensor(
				this.getSystemService(SENSOR_SERVICE));
		ps.RegisterListener();
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
	}
}
```
6.建立一个广播接收类，用于在开机启动完毕后启动我们的Service，并且监听去电获得去电电话号码
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent; /*
 [Tags]* 1.首先开机启动后系统会发出一个Standard Broadcast Action，
 [Tags]*    名字叫android.intent.action.BOOT_COMPLETED，这个Action只会发出一次。
 [Tags]* 2.构造一个BroadcastReceiver类，重构其抽象方法onReceive(Context context, Intent intent)，
 [Tags]*         在其中启动你想要启动的Service。
 [Tags]* 3.在AndroidManifest.xml中，首先加入
 [Tags]*    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"></uses-permission>
 [Tags]*    来获得BOOT_COMPLETED的使用许可，然后注册前面重构的IntentReceiver类，
 [Tags]*    在其<intent-filter>中加入<action android:name="android.intent.action.BOOT_COMPLETED" /> ，
 [Tags]*    以使其能捕捉到这个Action。       
 [Tags]* 參考文檔： 
 [Tags]*/
public class StartReceiver extends BroadcastReceiver {
	/* 要接收的intent源 */
	static final String ACTION = "android.intent.action.BOOT_COMPLETED";
	public void onReceive(Context context, Intent intent) {
		// 如果系統已經開機完畢，則啟動电话状态服務
		if (intent.getAction().equals(ACTION)) {
			// 启动电话状态服务
			context.startService(new Intent(context,
					PhoneCallStateService.class));
		}
		/*
		 [Tags]* intent.getAction().equals(Intent.ACTION_NEW_OUTGOING_CALL) 去电
		 [Tags]* intent.getAction().equals(Intent.ACTION_CALL)) 来电
		 [Tags]*/
		// 监听去电获得去电电话号码
		if (intent.getAction().equals(Intent.ACTION_NEW_OUTGOING_CALL)) {
			CommonHelper.outGoingPhoneNumber = intent
					.getStringExtra(Intent.EXTRA_PHONE_NUMBER);
		}
	}
}
```
7.由有上述类需要使用到一些权限，所以我们要在我们的AndroidManifest.xml配置文件中做如下修改：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android=" http://schemas.android.com/apk/res/android "
    package="com.org.speaker"
    android:versionCode="1"
    android:versionName="1.0" >
    <application android:icon="@drawable/icon" >
        <activity android:name="CustomerInfo" >
        </activity>
        <receiver android:name="StartReceiver" >
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />

                <category android:name="android.intent.category.LAUNCHER" />
                <!-- 添加过滤条件 监听电话状态 -->
                <action android:name="android.intent.action.PHONE_STATE" />
                <!-- 监听电话拨出 -->
                <action android:name="android.intent.action.NEW_OUTGOING_CALL" />
            </intent-filter>
        </receiver>
        <service
            android:name="PhoneCallStateService"
            android:enabled="true" >
        </service>
    </application>
    <uses-sdk android:minSdkVersion="4" />
    <!-- 连接互联网的权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- 读取手机状态的权限 -->
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <!-- 接收手机开机广播的权限 -->
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <!-- 更改音讯设置的权限 -->
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <!-- 使用SD卡的权限 这个是我个人用到的 大家可以不使用 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!-- 监听去电的权限 -->
    <uses-permission android:name="android.permission.PROCESS_OUTGOING_CALLS" />
    <!-- 修改电话状态的权限 -->
    <uses-permission android:name="android.permission.MODIFY_PHONE_STATE" />
    <!-- 监听来电的权限 -->
    <uses-permission android:name="android.permission.CALL_PHONE" />
</manifest>
```