这里我们主要就是介绍一下闪屏的。下面的例子里我们主要就是用到了android中的Activity;Intent;Bundle;util.Log;KeyEvent;这些，下面我们就看看例子中是怎么实现闪屏的吧。
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.KeyEvent;
public class test extends Activity {
	private long m_dwSplashTime = 3000;
	private boolean m_bPaused = false;
	private boolean m_bSplashActive = true;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.splash);
		Thread splashTimer = new Thread() {
			public void run() {
				try {
					// wait loop
					long ms = 0;
					while (m_bSplashActive && ms < m_dwSplashTime) {
						sleep(100);
						if (!m_bPaused)
							ms += 100;
					}
					startActivity(new Intent("com.google.app.splashy.CLEARSPLASH"));
				}
				catch (Exception ex) {
					Log.e("Splash", ex.getMessage());
				}
				finally {
					finish();
				}
			}
		};
		splashTimer.start();
	}
	@Override
	protected void onPause() {
		super.onPause();
		m_bPaused = true;
	}
	@Override
	protected void onResume() {
		super.onResume();
		m_bPaused = false;
	}
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		super.onKeyDown(keyCode, event);
		switch (keyCode) {
		case KeyEvent.KEYCODE_MENU:
			m_bSplashActive = false;
			break;
		case KeyEvent.KEYCODE_BACK:
			/* 两种退出方法 */
			/* System.exit(0); */
			/* android.os.Process.killProcess(android.os.Process.myPid()); */
			android.os.Process.killProcess(android.os.Process.myPid());
			break;
		default:
			break;
		}
		return true;
	}
}
```
AndroidManifest.xml 这里面配置很重要
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.demo"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name="test"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" >
                    <category android:name="android.intent.category.LAUNCHER" >
                    </category>
                </action>
            </intent-filter>
        </activity>
        <activity
            android:name=".MainMenu"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="com.google.app.splashy.CLEARSPLASH" >
                    <category android:name="android.intent.category.DEFAULT" >
                    </category>
                </action>
            </intent-filter>
        </activity>
    </application>
</manifest>
```