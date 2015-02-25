mail代码：
```  
<linearlayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:rientation="vertical" >
    <surfaceview
        android:id="@+id/surfaceView1"
        android:layout_width="640px"
        android:layout_height="480px"
        android:layout_gravity="center" >
    </surfaceview>
</linearlayout>
```
AndroidManifest.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package=""
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="7" />
    <uses-permission android:name="android.permission.CAMERA" >
    </uses-permission>
    <uses-feature android:name="android.hardware.camera.autofocus" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".CameraTest_4"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```