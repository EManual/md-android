Linux kernel启动以后会通过app_main进程来初始化android Runtime Java运行环境，而zygote是Android的第一个进程。所有的android的应用以及大部分系统服务都是通过zygote fork出来的子进程（我现在看到的只有native的service manager不是zygote fork出来的）。在system server中启动的若干系统服务中与我们启动进程相关的就是Acitivity Manager。
当systerm server启动好所有服务以后，系统就进入”system ready”状态，这个时候Activity Manager就登场了。Activity Manager光看代码行就知道是一个重量级的服务，它主要管理Activity之间的跳转，以及进程的生命周期。当Activity Manager发现系统已经启动好以后它就会发出一个intent：
```  
Intent intent = new Intent(mTopAction,
		mTopData != null ? Uri.parse(mTopData) : null);
intent.setComponent(mTopComponent);
if (mFactoryTest != SystemServer.FACTORY_TEST_LOW_LEVEL) {
	intent.addCategory(Intent.CATEGORY_HOME);
} 
```
通过这个category类型为home的intent，Activity Manager就会通过：
```  
startActivityLocked(null, intent, null, null, 0, aInfo, null, null, 0, 0, 0, false, false); 
```
启动Home进程了。而这个启动Home进程的过程实际上还是去通过zygote fork出的一个子进程。
因此只要在manifest中具备这样的intent－filter都可以在开机的时候作为Home启动：
```  
<intent-filter> 
	<action android:name="android.intent.action.MAIN" />
	<category android:name="android.intent.category.HOME"/>
	<category android:name="android.intent.category.DEFAULT" />
</intent-filter>	
```
多个home之间的switch会在开始的时候有个选择，至于这个选择好像是package manager来实现的，没有仔细研究过。
#### 2.UI结构
通过launcher/Res/Layout-land/launcher.xml分析可以得到主屏幕的UI结构：
![img](P)  
整个homescreen是一个包含三个child view的FrameLayout(com.android.launcher.DragLayer)。
第一个child就是桌面com.android.launcher.Workspace。这个桌面又包含三个child。每个child就对应一个桌面。这就是你在Android上看到的三个桌面。每个桌面上可以放置下列对象：应用快捷方式，appwidget和folder。
第二个child是一个SlidingDrawer控件，这个控件由两个子控件组成。一个是com.android.launcher.HandleView，就是Android桌面下方的把手，当点击这个把手时，另一个子控件，com.android.launcher.AllAppsGridView就会弹出，这个子控件列出系统中当前安装的所有类型为category.launcher的Activity。
第三个child是com.android.launcher.DeleteZone.当用户在桌面上长按一个widget时，把手位置就会出现一个垃圾桶形状的控件，就是这个控件。