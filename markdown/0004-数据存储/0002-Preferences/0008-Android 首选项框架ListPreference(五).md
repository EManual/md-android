在下边就是我们的AndroidManifest.xml文件了，倒也没啥特别的。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package=""
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="10" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".MainActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".FlightPreferenceActivity"
            android:label="@string/prefTitle" >
            <intent-filter>
                <action android:name="xiaohang.zhimeng.intent.action.FlightPreferences" />
                <category android:name="android.intent.category.PREFERENCE" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```
当我们完成了上边的所有运行应用程序，首先会看到一个简单的文本消息，显示“option value is 1( of Stops)”。单击Menu按钮，然后在点击Settings,就会打开我们的首选项视图FlightPreferenceActivity，然后我们更改首选项的值，然后再点击back按钮就会看到我们修改后的值了。
大家可能会问，那Android把我们修改后的数据存储在哪里了呢?前面已经提到Android framework还会负责持久化首选项。例如，当用户选择一个排序选项时，Android会选择存储在应用程序 /data 目录下的一个XML 文件中，见下图。
实际的文件路径为/data/data/[PACKAGE_NAME]/shared_prefs/[PACKAGE_NAME]_preferences.xml。我们需要看看这个文件里边到底存了些什么?导出这个文件就可以看到了。不对，不用这样太麻烦了，我们去shell里边用cat读一下就行了，见下图。
![img](P)  