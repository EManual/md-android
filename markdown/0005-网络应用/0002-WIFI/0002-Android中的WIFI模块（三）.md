#### 3.配置 AP 参数
当用户在 WifiSettings 界面上选择了一个 AP 后，会显示配置 AP 参数的一个对话框，
```  
public boolean onPreferenceTreeClick(PreferenceScreen preferenceScreen,
		Preference preference) {
	if (preference instanceof AccessPointPreference) {
		AccessPointState state = preference.getAccessPointState();
		state = (AccessPointPreference) showAccessPointDialog(state,
				AccessPointDialog.MODE_INFO);
	}
} 
```
#### 4.连接
当用户在AcessPointDialog中选择好加密方式和输入密钥之后，再点击连接按钮，Android就会去连接这个 AP。
```  
private void handleConnect() {
	String password = getEnteredPassword();
	if (!TextUtils.isEmpty(password)) {
		mState.setPassword(password);
	}
	mWifiLayer.connectToNetwork(mState);
}
```
WifiLayer会先检测这个AP是不是之前被配置过，这个是通过向wpa_supplicant发送LIST_NETWORK 命令并且比较返回值来实现的，
```  
// Need WifiConfiguration for the AP
WifiConfiguration config = findConfiguredNetwork(state);
```
如果wpa_supplicant没有这个AP的配置信息，则会向wpa_supplicant发送ADD_NETWORK命令来添加该 AP，
```  
if (config == null) { 
	// Connecting for the first time, need to create it
	config = addConfiguration(state, ADD_CONFIGURATION_ENABLE|ADD_CONFIGURATION_SAVE);
} 
```
ADD_NETWORK命令会返回一个ID ，WifiLayer再用这个返回的ID作为参数向
```  
// wpa_supplicant 发送 ENABLE_NETWORK 命令，从而让 wpa_supplicant 去连接该 AP。
// Make sure that network is enabled, and disable others
mReenableApsOnNetworkStateChange = true;
if (!mWifiManager.enableNetwork(state.networkId, true)) {
	Log.e(TAG, "Could not enable network ID " + state.networkId);
	error(R.string.error_connecting);
	return false;
}
```
#### 5.配置IP地址
当wpa_supplicant成功连接上AP之后，它会向控制通道发送事件通知连接上AP了，从而wifi_wait_for_event函数会接收到该事件，由此WifiMonitor中的MonitorThread会被执行来出来这个事件，
```  
void handleEvent(int event, String remainder) {
	case CONNECTED: 
		handleNetworkStateChange(NetworkInfo.DetailedState.CONNECTED, remainder);
		break; 
}
```