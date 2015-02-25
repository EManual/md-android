#### 3.应用程序代码分析
由Launcher中的AndroidManifest.xml可以看出整个Launcher的代码结构。
![img](P)  
首先，是一些权限的声明。例如：
```  
<uses-permission android:name="android.permission.CALL_PHONE" /> 
<uses-permission android:name="android.permission.EXPAND_STATUS_BAR" /> 
```
这部分可以略过；
其次，Application的构成，如上图：
(1)Launcher：HomeScreen的Activity。
```  
<intent-filter> 
	<action android:name="android.intent.action.MAIN" />
	<category android:name="android.intent.category.HOME"/>
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.MONKEY" />
</intent-filter>
```
上面这段代码就标志着它是开机启动后Home的Activity。通过Launcher.java中onCreat()的分析我们可以大致把握屏幕的主要活动：
```  
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	// 把xml文件的内容实例化到View中
	mInflater = getLayoutInflater();
	// 监听应用程序控件改变事件
	mAppWidgetManager = AppWidgetManager.getInstance(this);
	mAppWidgetHost = new LauncherAppWidgetHost(this, APPWIDGET_HOST_ID);
	mAppWidgetHost.startListening();
	// 用于调试?
	if (PROFILE_STARTUP) {
		android.os.Debug.startMethodTracing("/sdcard/launcher");
	}
	// 监听locale，mcc，mnc是否改变，如果改变，则重写新配置
	// mcc：mobile country code(国家代码China 460); mnc：mobile network code(网络代码)
	checkForLocaleChange();
	/*
	 [Tags]* This allows such applications to have a virtual wallpaper that is
	 [Tags]* larger than the physical screen, matching the size of their
	 [Tags]* workspace.
	 [Tags]*/
	setWallpaperDimension();
	// 显示主屏幕UI元素，workspace,slidingdrawer(handleview and
	// appgridview),deletezone
	setContentView(R.layout.launcher);
	// Finds all the views we need and configure them properly.
	// 完成workspace，slidingdrawer，deletezone的各种事件操作和监听
	setupViews();
	// Registers various intent receivers.
	// 允许其他应用对本应用的操作
	registerIntentReceivers();
	// Registers various content observers.
	// 例如，注册一个内容观察者跟踪喜爱的应用程序
	registerContentObservers();
	// 重新保存前一个状态（目的??）
	mSavedState = savedInstanceState;
	restoreState(mSavedState);
	// 调试?
	if (PROFILE_STARTUP) {
		android.os.Debug.stopMethodTracing();
	}
	// Loads the list of installed applications in mApplications.
	if (!mRestoring) {
		startLoaders();
	}
	// For handling default keys??
	mDefaultKeySsb = new SpannableStringBuilder();
	Selection.setSelection(mDefaultKeySsb, 0);
}
```