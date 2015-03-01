在很多时候，我们需要给一个Layout设置一个背景。例如，我们下下面的layout中使用了这样一个背景:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/antelope_canyon"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/TextView01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@+id/TextView01" >
    </TextView>
</LinearLayout>
```
其中的LinearLayout使用了 背景图片antelope_canyon。
如果仔细观察程序的运行过过程，我们首先看到了黑色的activity背景，然后才看到背景图被加载，那是因为在activity start以后，我们才能调用setContentView设置我们的layout，然后才绘制我们在layout中放置的背景图。而在此之前，程序中绘制的是android中默认黑色背景。 这样会给用户感觉我们的activity启动较慢。
然而，如果将背景图定义在一个主题中，如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="Theme.Droidus" parent="android:Theme">
		<item name="android:windowBackground">@drawable/antelope_canyon</item>
		<item name="android:windowNoTitle">true</item>
	</style>
</resources>
```
然后在activity中使用这个主题：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.droidus"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".SpeedUpStartupActivity"
            android:label="@string/app_name"
            android:theme="@style/Theme.Droidus" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="4" />
</manifest>
```
运行程序，可以看到背景图马上显示了，没有再看到黑色的背景图。
为什么会有这样的现象呢？那是因为 程序的主题是在程序启动的时候加载的，而不是在activity启动之后加载！
而如果在layout使用背景，背景图是在activity启动之后才加载，故而会让用户看到一个黑色背景闪动的过程。