制作一张启动图片splash.png，放置在res->drawable-hdpi文件夹中。
新建布局文件splash.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/splash"
    android:gravity="bottom|center"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/versionNumber"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="20dip"
        android:gravity="bottom"
        android:shadowColor="FFFFFF"
        android:shadowDx="0"
        android:shadowDy="2"
        android:shadowRadius="1"
        android:text="@+id/TextView01"
        android:textColor="444444"
        android:textSize="20dip"
        android:typeface="sans" >
    </TextView>
</LinearLayout>
```
这里我们把上一步制作的图片作为启动界面的背景图，然后在界面底部显示当前程序的版本号。
新建SplashActivity，在Oncreate中添加以下代码：
```  
setContentView(R.layout.splash);
PackageManager pm = getPackageManager();
try {
	PackageInfo pi = pm.getPackageInfo("com.lyt.android", 0);
	TextView versionNumber = (TextView) findViewById(R.id.versionNumber);
	versionNumber.setText("Version " + pi.versionName);
} catch (NameNotFoundException e) {
	e.printStackTrace();
}
new Handler().postDelayed(new Runnable() {
	@Override
	public void run() {
		Intent intent = new Intent(SplashActivity.this,
				SplashScreenActivity.class);
		startActivity(intent);
		SplashActivity.this.finish();
	}

}, 2500);
```
4、修改Manifest文件，将启动界面Activity改为默认启动，并且设置标题栏不可见。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package=""
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".SplashActivity"
            android:label="@string/app_name"
            android:theme="@android:style/Theme.NoTitleBar" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".SplashScreenActivity"
            android:label="@string/app_name" >
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="8" />
</manifest>
```