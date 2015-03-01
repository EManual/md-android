WifiMonitor 再调用 WifiStateTracker 的 notifyStateChange，WifiStateTracker 则接着会往自身发送 EVENT_DHCP_START 消息来启动DHCP去获取IP地址，
```  
private void handleConnectedState() {
	setPollTimer();
	mLastSignalLevel = -1;
	if (!mHaveIPAddress && !mObtainingIPAddress) {
		mObtainingIPAddress = true;
		mDhcpTarget.obtainMessage(EVENT_DHCP_START).sendToTarget();
	}
}
```
然后再广播发送 NETWORK_STATE_CHANGED_ACTION 这个 Intent
```  
case EVENT_NETWORK_STATE_CHANGED:
	if (result.state != DetailedState.DISCONNECTED || !mDisconnectPending) {
		intent = new 
		Intent(WifiManager.NETWORK_STATE_CHANGED_ACTION);
		intent.putExtra(WifiManager.EXTRA_NETWORK_INFO, mNetworkInfo);
		if (result.BSSID != null) 
		intent.putExtra(WifiManager.EXTRA_BSSID, result.BSSID);
		mContext.sendStickyBroadcast(intent);
	} 
	break; 
```
WifiLayer 注册了接收 NETWORK_STATE_CHANGED_ACTION 这个 Intent，所以它的相关处理函数 handleNetworkStateChanged 会被调用，当DHCP 拿到IP 地址之后，会再发送EVENT_DHCP_SUCCEEDED消息，
```  
private class DhcpHandler extends Handler {
	public void handleMessage(Message msg) {
		switch (msg.what) {
		case EVENT_DHCP_START:
			if (NetworkUtils.runDhcp(mInterfaceName, mDhcpInfo)) {
				event = EVENT_DHCP_SUCCEEDED;
			}
		}
	}
}
```
WifiLayer处理EVENT_DHCP_SUCCEEDED消息 ，会再次广播发送NETWORK_STATE_CHANGED_ACTION 这个 Intent，这次带上完整的 IP 地址信息。
```  
case EVENT_DHCP_SUCCEEDED:
	mWifiInfo.setIpAddress(mDhcpInfo.ipAddress);
	setDetailedState(DetailedState.CONNECTED);
	intent = new Intent(WifiManager.NETWORK_STATE_CHANGED_ACTION);
	intent.putExtra(WifiManager.EXTRA_NETWORK_INFO, mNetworkInfo);
	mContext.sendStickyBroadcast(intent);
	break; 
```