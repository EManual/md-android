配置文件如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.android"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="8" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".ActToggleGPS"
            android:label="@string/app_name"
            android:sharedUserId="android.uid.system" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.WRITE_SECURE_SETTINGS" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
</manifest>
```
代码如下：
```  
import java.lang.reflect.Method;
import android.app.Activity;
import android.app.PendingIntent;
import android.app.PendingIntent.CanceledException;
import android.content.ContentResolver;
import android.content.Context;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class ActToggleGPS extends Activity implements OnClickListener {
	private static final int BLUETOOTH = 4;
	private static final int BRIGHTNESS = 1;
	private static final int GPS = 3;
	private static final int SYNC = 2;
	private static final int WIFI = 0;
	// private TextView txtInfo;
	private Button btnGPS, btnWifi, btnSync, btnBrightness, btnBluetooth;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.liner);
		btnGPS = (Button) findViewById(R.id.toggleGPS);
		btnGPS.setOnClickListener(this);
		btnBluetooth = (Button) findViewById(R.id.toggleBluetooth);
		btnBluetooth.setOnClickListener(this);
		btnBrightness = (Button) findViewById(R.id.toggleBrightness);
		btnBrightness.setOnClickListener(this);
		btnSync = (Button) findViewById(R.id.toggleSync);
		btnSync.setOnClickListener(this);
		btnWifi = (Button) findViewById(R.id.toggleWifi);
		btnWifi.setOnClickListener(this);
		// txtInfo = (TextView) findViewById(R.id.info);
	}
	public static void toggle(Context context, int idx) {
		Intent intent = new Intent();
		intent.setClassName("com.android.settings",
				"com.android.settings.widget.SettingsAppWidgetProvider");
		intent.addCategory("android.intent.category.ALTERNATIVE");
		Uri uri = Uri.parse("custom:" + idx);
		intent.setData(uri);
		try {
			PendingIntent.getBroadcast(context, 0, intent, 0).send();
		} catch (CanceledException e) {
			e.printStackTrace();
		}
	}
	public boolean checkGPS(String key) {
		try {
			Class secureClass = Class
					.forName("android.provider.Settings$Secure");
			Method isMethod = secureClass.getMethod(
					"isLocationProviderEnabled", ContentResolver.class,
					String.class);
			Boolean result = (Boolean) isMethod.invoke(secureClass,
					this.getContentResolver(), "gps");
			Log.d("ANDROID_LAB", "gps=" + result);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SecurityException e) {
			e.printStackTrace();
		} catch (NoSuchMethodException e) {
			e.printStackTrace();
		} catch (IllegalArgumentException e) {
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		} catch (InvocationTargetException e) {
			e.printStackTrace();
		}
		return false;
	}
	@Override
	public void onClick(View v) {
		if (v == btnGPS) {
			toggle(this, GPS);
		} else if (v == btnWifi) {
			toggle(this, WIFI);
		} else if (v == btnSync) {
			toggle(this, SYNC);
		} else if (v == btnBluetooth) {
			toggle(this, BLUETOOTH);
		} else if (v == btnBrightness) {
			toggle(this, BRIGHTNESS);
		}
	}
}
```