下面的代码才是开发AppWidget用到的代码
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!--
		<ImageView
		xmlns:android="http://schemas.android.com/apk/res/android"
		android:id="@+id/imageView"
		android:gravity="center"
		android:layout_width="fill_parent"
		android:layout_height="wrap_content" />
    -->
    <Button
        android:id="@+id/btn"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="@drawable/png1"
        android:gravity="center" />
</LinearLayout>
```
my_appwidget.xml布局文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<!--
	AppWidgetProvderInfo: 描述AppWidget的大小、更新频率和初始界面等信息，以XML文件形式存在于应用的res/xml/目录下。
	注意：SDK1.5之后此android:updatePeriodMillis就失效了，要自己创建service更新
-->
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:initialLayout="@layout/my_layout"
    android:minHeight="45dip"
    android:minWidth="75dip"
    android:updatePeriodMillis="1000" />
```
清单文件
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="eoe.demo"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".MainActivity"
            android:label="主程序" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!-- TestActivity类为一个广播接收器，因为TestActivity继承自AppWidgetProvider -->
        <receiver
            android:name=".TestActivity"
            android:label="添加桌面控件" >
            <intent-filter>
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
            </intent-filter>
            <meta-data
                android:name="android.appwidget.provider"
                android:resource="@xml/my_appwidget" />
        </receiver>
    </application>
    <uses-sdk android:minSdkVersion="7" />
</manifest>
```