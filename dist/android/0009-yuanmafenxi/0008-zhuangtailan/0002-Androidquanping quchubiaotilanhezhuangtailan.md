在开发中我们经常需要把我们的应用设置为全屏，这里我所知道的有俩中方法，一中是在代码中设置，另一种方法是在配置文件里改！
#### 一、在代码中设置：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;
public class OpenGl_Lesson1 extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 去除title
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		// 去掉Activity上面的状态栏
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		setContentView(R.layout.main);
	}
}
```
在这里要强调一点，设置全屏的俩段代码必须在setContentView（R.layout.main） 之前，不然会报错.
#### 二、在配置文件里修改
（关键代码：android：theme="@android：style/Theme.NoTitleBar.Fullscreen"，如果想只是去除标题栏就后面不用加Fullscreen了，另外，如果想要整个应用都去除标题栏和状态栏，就把这句代码加到<application..标签里面，如果只是想某个activity起作用，这句代码就加到相应的activity上）：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.android.tutor"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".OpenGl_Lesson1"
            android:label="@string/app_name"
            android:theme="@android:style/Theme.NoTitleBar.Fullscreen" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="7" />
</manifest>
```