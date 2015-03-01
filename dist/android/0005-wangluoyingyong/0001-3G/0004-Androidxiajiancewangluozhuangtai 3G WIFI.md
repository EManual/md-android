代码如下：
```  
package Android.HelloAndroid;    
import android.app.Activity;
import android.app.AlertDialog;
import android.content.Context;
import android.os.Bundle;
import android.widget.TextView;
import android.net.ConnectivityManager;
import android.net.NetworkInfo;
import android.net.NetworkInfo.State;
public class Hello extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		TextView tv = new TextView(this);
		tv.setText("检测网络状态");
		setContentView(tv);
		checkNetworkInfo();
		goodNet();
	}
	public boolean goodNet() {
		ConnectivityManager manager = (ConnectivityManager) getSystemService(CONNECTIVITY_SERVICE);
		NetworkInfo networkinfo = manager.getActiveNetworkInfo();
		if (networkinfo == null || !networkinfo.isAvailable()) {
			new AlertDialog.Builder(this).setMessage("没有可以使用的网络")
					.setPositiveButton("Ok", null).show();
			return false;
		}
		new AlertDialog.Builder(this).setMessage("网络正常可以使用")
				.setPositiveButton("Ok", null).show();
		return true;
	}
	private void checkNetworkInfo() {
		ConnectivityManager conMan = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE); 
		// mobile
		// 3G
		// Data
		// Network
		State mobile = conMan.getNetworkInfo(ConnectivityManager.TYPE_MOBILE)
				.getState();
		new AlertDialog.Builder(this).setMessage(mobile.toString())
				.setPositiveButton("3G", null).show();// 显示3G网络连接态
		State wifi = conMan.getNetworkInfo(ConnectivityManager.TYPE_WIFI)
				.getState();
		new AlertDialog.Builder(this).setMessage(wifi.toString())
				.setPositiveButton("WIFI", null).show();// 显示wifi网络连接状态
	}
}
```
需要注意:
根据 android的安全机制，在使用ConnectivityManager时，必须在AndroidManifest.xml中添加<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 否则无法获得系统的许可。