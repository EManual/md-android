#### 1.什么是WIFI
答：WIFI是一种无线联网的技术，无线传输协议，以前通过网线连接电脑，而现在是通过无线电波来联网；常见的就是一个无线路由器，在这个无线路由器的电波覆盖的有效范围都可以采用WIFI连接方式进行联网，如果无线路由器连接了一条ASDL线路或者别的上网线路，则又被称为”热点”。
#### 2.获取WIFI网卡的状态
答：WIFI网卡的状态是由一系列的整形常量来表示的：
```  
WIFI_STATE_DISABLED：  WIFI网卡不可用
WIFI_STATE_DISABLING： WIFI正在关闭
WIFI_STATE_ENABLED： WIFI网卡可用 
WIFI_STATE_ENABLING：WIFI网卡正在打开
WIFI_STATE_UNKNOWN: 未知网卡状态
```
#### 3.改变WIFI网卡的状态
答：对WIFI网卡进行操作需要通过WifiManager对象来进行，获取方法如下；
WifiManager  wifiManager=( WifiManager )Context.getSystemService(Service.WIFI_SERVICE);
一般开发中，Context是Activity的父类，上面通常由Activity.this代替了。同理Service由Context代替了。
打开WIFI网卡
wifiManager.setWifiEnabled(true);
关闭WIFI网卡
wifiManager.setWifiEnabled(false);
获取网卡当前状态
wifiManager.getWifiState();
在AndroidManifest.xml进行对WIFI操作的权限设置
```  
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```
代码如下：
```  
import android.app.Activity;
import android.content.Context;
import android.net.wifi.WifiManager;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.Toast;
public class WifiActivity extends Activity {
	private Button startWifi = null;
	private Button endWifi = null;
	private Button checkWifi = null;
	private WifiManager wifimanager = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		startWifi = (Button) findViewById(R.id.startWifi);
		endWifi = (Button) findViewById(R.id.closeWifi);
		checkWifi = (Button) findViewById(R.id.checkWifi);
		startWifi.setOnClickListener(new startWifiListener());
		endWifi.setOnClickListener(new endWifiListener());
		checkWifi.setOnClickListener(new checkWifiListener());
	}
	class startWifiListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			wifimanager = (WifiManager) WifiActivity.this
					.getSystemService(Context.WIFI_SERVICE);
			wifimanager.setWifiEnabled(true);
			System.out.println("wifi state------->
					+ wifimanager.getWifiState());
			Toast.makeText(WifiActivity.this, "当前WIFI网卡的状态为",
					wifimanager.getWifiState());
		}
	}
	class endWifiListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			wifimanager = (WifiManager) WifiActivity.this
					.getSystemService(Context.WIFI_SERVICE);
			wifimanager.setWifiEnabled(false);
			System.out.println("wifi state------->
					+ wifimanager.getWifiState());
			Toast.makeText(WifiActivity.this, "当前WIFI网卡的状态为",
					wifimanager.getWifiState());
		}
	}
	class checkWifiListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			wifimanager = (WifiManager) WifiActivity.this
					.getSystemService(Context.WIFI_SERVICE);
			System.out.println("wifi state------->
					+ wifimanager.getWifiState());
			Toast.makeText(WifiActivity.this, "当前WIFI网卡的状态为",
					wifimanager.getWifiState());
		}
	}
}
```