#### 初始化
在SystemServer启动的时候，会生成一个ConnectivityService的实例，
```  
try {
	Log.i(TAG, "Starting Connectivity Service.");
	ServiceManager.addService(Context.CONNECTIVITY_SERVICE,
			new ConnectivityService(context));
} catch (Throwable e) {
	Log.e(TAG, "Failure starting Connectivity Service", e);
}
```
ConnectivityService 的构造函数会创建 WifiService，
```  
if (DBG)
	Log.v(TAG, "Starting Wifi Service.");
mWifiStateTracker = new WifiStateTracker(context, handler);
WifiService wifiService = new WifiService(context, mWifiStateTracker);
ServiceManager.addService(Context.WIFI_SERVICE, wifiService);
```
WifiStateTracker 会创建 WifiMonitor 接收来自底层的事件，WifiService 和 WifiMonitor 是整个模块的核心。WifiService 负责启动关闭 wpa_supplicant、启动关闭 WifiMonitor 监视线程和把命令下发给 wpa_supplicant，而 WifiMonitor 则负责从 wpa_supplicant 接收事件通知。
#### 连接 AP
1. 使用WIFI
WirelessSettings 在初始化的时候配置了由 WifiEnabler 来处理 Wifi 按钮，
```  
private void initToggles() {
	mWifiEnabler = new WifiEnabler(this,
			(WifiManager) getSystemService(WIFI_SERVICE),
			(CheckBoxPreference) findPreference(KEY_TOGGLE_WIFI));
}
```
当用户按下Wifi 按钮后，Android 会调用 WifiEnabler 的 onPreferenceChange，再由 WifiEnabler调用WifiManager 的 setWifiEnabled 接口函数，通过 AIDL，实际调用的是WifiService的setWifiEnabled函数，WifiService接着向自身发送一条MESSAGE_ENABLE_WIFI消息，在处理该消息的代码中做真正的使能工作：首先装载WIFI内核模块（该模块的位置硬编码为"/system/lib/modules/wlan.ko"），然后启动wpa_supplicant（配置文件硬编码为"/data/misc/wifi/wpa_supplicant.conf"），再通过WifiStateTracker来启动WifiMonitor中的监视线程。
```  
private boolean setWifiEnabledBlocking(boolean enable) {
	final int eventualWifiState = enable ? WIFI_STATE_ENABLED
			: WIFI_STATE_DISABLED;
	updateWifiState(enable ? WIFI_STATE_ENABLING : WIFI_STATE_DISABLING);
	if (enable) {
		if (!WifiNative.loadDriver()) {
			Log.e(TAG, "Failed to load Wi-Fi driver.");
			updateWifiState(WIFI_STATE_UNKNOWN);
			return false;
		}
		if (!WifiNative.startSupplicant()) {
			WifiNative.unloadDriver();
			Log.e(TAG, "Failed to start supplicant daemon.");
			updateWifiState(WIFI_STATE_UNKNOWN);
			return false;
		}
		mWifiStateTracker.startEventLoop();
	}
	// Success!
	persistWifiEnabled(enable);
	updateWifiState(eventualWifiState);
	return true;
}	
```