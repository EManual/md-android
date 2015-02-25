在应用程序载入之前一般都有Splash图片，在android上实现如下：
Splash.java
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.os.Handler;
public class Splash extends Activity {
	[Tags]/**
	 [Tags]* 延期时间
	 [Tags]*/
	private final int SPLASH_DISPLAY_LENGHT = 5000;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.splash);
		[Tags]/**
		 [Tags]* 使用handler来处理
		 [Tags]*/
		new Handler().postDelayed(new Runnable() {
			@Override
			public void run() {
				Intent mainIntent = new Intent(Splash.this, Main.class);
				Splash.this.startActivity(mainIntent);
				Splash.this.finish();
			}

		}, SPLASH_DISPLAY_LENGHT);
	}
}
```
AndroidManifest.xml
代码:
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.sunshine.splash"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".Splash"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name="Main" >
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="3" />
</manifest>
```