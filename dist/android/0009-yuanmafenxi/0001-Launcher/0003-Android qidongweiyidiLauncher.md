如果你要定制一个Android系统，你想用你自己的Launcher(Home)作主界面来替换Android自己的Home，而且不希望用户安装的Launcher来替换掉你的Launcher。我们可以通过修改Framework来实现这样的功能。
这里以Android2.1的源代码为例来实际说明。
#### 1)首先了解一下Android的启动过程。
Android系统的启动先从Zygote开始启动，然后……(中间的过程就不说了)。一直到了SystemServer(framework)这个地方，看到这段代码：
```  
native public static void init1(String[] args);
public static void main(String[] args) {
	if (SamplingProfilerIntegration.isEnabled()) {
		SamplingProfilerIntegration.start();
		timer = new Timer();
		timer.schedule(new TimerTask() {
			@Override
			public void run() {
				SamplingProfilerIntegration.writeSnapshot("system_server");
			}
		}, SNAPSHOT_INTERVAL, SNAPSHOT_INTERVAL);
	}
	// 该系统服务器必须运行所有的时间,所以需要的话
	// 有效的尽可能与它的内存使用.
	VMRuntime.getRuntime().setTargetHeapUtilization(0.8f);
	System.loadLibrary("android_servers");
	init1(args);
}
public static final void init2() {
	Log.i(TAG, "Entered the Android system server!");
	Thread thr = new ServerThread();
	thr.setName("android.server.ServerThread");
	thr.start();
}
```
从SystemServer的main函数开始启动各种服务。首先启动init1,然后启动init2.从上面的注释可以看到：init1这个方法时被Zygote调用来初始化系统的，init1会启动native的服务如SurfaceFlinger,AudioFlinger等等，这些工作做完以后会回调init2来启动Android的service。
这里我们主要来关注init2的过程。init2中启动ServerThread线程，ServerThread中启动了一系列的服务，比如这些：
```  
ActivityManagerService
EntropyService
PowerManagerService
TelephonyRegistry
PackageManagerService
AccountManagerService
BatteryService
HardwareService
Watchdog
SensorService
BluetoothService
StatusBarService
ClipboardService
InputMethodManagerService
NetStatService
ConnectivityService
AccessibilityManagerService
NotificationManagerService
MountService
DeviceStorageMonitorService
LocationManagerService
SearchManagerService
FallbackCheckinService
WallpaperManagerService
AudioService
BackupManagerService
AppWidgetService
```
我们还要设置一些权限就可以了。
```  
<application
    android:icon="@drawable/icon"
    android:label="@string/app_name"
    android:process="android.process.acore3" >
    <activity
        android:name=".FirstAppActivity"
        android:label="@string/app_name" >
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />
            <category android:name="android.intent.category.HOME_FIRST" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.MONKEY" />
        </intent-filter>
    </activity>
</application>
```