#### 1、需要申请的权限
```  
android.permission.ACCESS_WIFI_STATE 
android.permission.CHANGE_WIFI_STATE 
android.permission.WAKE_LOCK
```
#### 2、相关操作的代码
```  
WifiManager wifi = (WifiManager) context
				.getSystemService(Context.WIFI_SERVICE);
if (!wifi.isWifiEnabled()) { // 如果wifi没有开启，则开启。
	wifi.setWifiEnabled(true);
}
WifiConfiguration wc = new WifiConfiguration();
wc.SSID = "\"SSID\""; // 配置wifi的SSID，即该热点的名称，如：TP-link_xxx
wc.preSharedKey = "\"password\""; // 该热点的密码
wc.hiddenSSID = true;
wc.status = WifiConfiguration.Status.ENABLED;
wc.allowedGroupCiphers.set(WifiConfiguration.GroupCipher.TKIP);
wc.allowedGroupCiphers.set(WifiConfiguration.GroupCipher.CCMP);
wc.allowedKeyManagement.set(WifiConfiguration.KeyMgmt.WPA_PSK);
wc.allowedPairwiseCiphers.set(WifiConfiguration.PairwiseCipher.TKIP);
wc.allowedPairwiseCiphers.set(WifiConfiguration.PairwiseCipher.CCMP);
wc.allowedProtocols.set(WifiConfiguration.Protocol.RSN);
int res = wifi.addNetwork(wc);
Log.d("WifiPreference", "add Network returned " + res);
boolean b = wifi.enableNetwork(res, true);
Log.d("WifiPreference", "enableNetwork returned " + b);
```
#### 3、注意
如果遇到force-close，选wait即可，因为启动wifi需要几秒钟，UI如果5妙钟还没反映的话，系统会给你这个force close exception。