当使能成功后，会广播发送WIFI_STATE_CHANGED_ACTION这个Intent通知外界WIFI已经成功使能了。WifiEnabler创建的时候就会向Android注册接收WIFI_STATE_CHANGED_ACTION，因此它会收到该 Intent，从而开始扫描。
```  
private void handleWifiStateChanged(int wifiState) {
	if (wifiState == WIFI_STATE_ENABLED) {
		loadConfiguredAccessPoints();
		attemptScan();
	}
}
```
#### 2.查找 AP
扫描的入口函数是 WifiService 的 startScan，它其实也就是往 wpa_supplicant 发送 SCAN 命令。
```  
static jboolean android_net_wifi_scanCommand(JNIEnv* env, jobject clazz)
{
	jboolean result;
	// Ignore any error from setting the scan mode.
	// The scan will still work.
	(void)doBooleanCommand("DRIVER SCAN-ACTIVE", "OK");
	result = doBooleanCommand("SCAN", "OK");
	(void)doBooleanCommand("DRIVER SCAN-PASSIVE", "OK");
	return result;
}
```
当wpa_supplicant处理完SCAN命令后，它会向控制通道发送事件通知扫描完成，从而wifi_wait_for_event 函数会接收到该事件，由此 WifiMonitor 中的 MonitorThread 会被执行来出来这个事件，
```  
void handleEvent(int event, String remainder) {
	case SCAN_RESULTS: 
		mWifiStateTracker.notifyScanResultsAvailable();
		break; 		
}
```
WifiStateTracker 则接着广播发送 SCAN_RESULTS_AVAILABLE_ACTION 这个 Intent
```  
case EVENT_SCAN_RESULTS_AVAILABLE:
	mContext.sendBroadcast(new 
	Intent(WifiManager.SCAN_RESULTS_AVAILABLE_ACTION));
```
WifiLayer 注册了接收SCAN_RESULTS_AVAILABLE_ACTION这个Intent，所以它的相关处理函数handleScanResultsAvailable会被调用，在该函数中，先会去拿到 SCAN 的结果（最终是往wpa_supplicant 发送 SCAN_RESULT 命令并读取返回值来实现的），
```  
List<ScanResult> list = mWifiManager.getScanResults();
```
对每一个扫描返回的 AP，WifiLayer 会调用 WifiSettings 的 onAccessPointSetChanged 函数，从而最终把该 AP 加到 GUI 显示列表中。
```  
public void onAccessPointSetChanged(AccessPointState ap, boolean added) {
	AccessPointPreference pref = mAps.get(ap);
	if (added) {
		if (pref == null) {
			pref = new AccessPointPreference(this, ap);
			mAps.put(ap, pref);
		} else {
			pref.setEnabled(true);
		}
		mApCategory.addPreference(pref);
	}
}
```