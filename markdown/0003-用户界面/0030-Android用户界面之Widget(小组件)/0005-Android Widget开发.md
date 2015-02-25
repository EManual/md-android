一、新建一个Android工程命名为:WidgetDemo.
二、准备素材，一个是Widget的图标，一个是Widget的背景。
三、修改string.xml文件。
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<string name="hello">Hello World, WidetDemo!</string>
	<string name="app_name">DaysToWorldCup</string>
</resources>
```
四、建立Widget内容提供者文件，我们在res下建立xml文件夹，并且新建一个widget_provider.xml。
```  
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android" 
	android:minWidth="50dip"
	android:minHeight="50dip"
	android:updatePeriodMillis="10000"
	android:initialLayout="@layout/main"
/>
```
五、修改main.xml布局。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/wordcup"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/wordcup"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello"
        android:textColor="ff0000"
        android:textSize="12px" />
</LinearLayout>
```
六、修改WidgetDemo.java
```  
import java.util.Calendar;
import java.util.Date;
import java.util.GregorianCalendar;
import java.util.Timer;
import java.util.TimerTask;
import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.ComponentName;
import android.content.Context;
import android.widget.RemoteViews;
public class WidetDemo extends AppWidgetProvider {
	@Override
	public void onUpdate(Context context, AppWidgetManager appWidgetManager,
			int[] appWidgetIds) {
		Timer timer = new Timer();
		timer.scheduleAtFixedRate(new MyTime(context, appWidgetManager), 1, 60000);
		super.onUpdate(context, appWidgetManager, appWidgetIds);
	}
	private class MyTime extends TimerTask {
		RemoteViews remoteViews;
		AppWidgetManager appWidgetManager;
		ComponentName thisWidget;
		public MyTime(Context context, AppWidgetManager appWidgetManager) {
			this.appWidgetManager = appWidgetManager;
			remoteViews = new RemoteViews(context.getPackageName(), R.layout.main);
			thisWidget = new ComponentName(context, WidetDemo.class);
		}
		public void run() {
			Date date = new Date();
			Calendar calendar = new GregorianCalendar(2010, 06, 11);
			long days = (((calendar.getTimeInMillis() - date.getTime()) / 1000)) / 86400;
			remoteViews.setTextViewText(R.id.wordcup, "距离南非世界杯还有" + days + "天");
			appWidgetManager.updateAppWidget(thisWidget, remoteViews);
		}
	}
}
```
七、修改配置文件AndroidManifest.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.android.tutor"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <receiver
            android:name=".WidetDemo"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
            </intent-filter>
            <meta-data
                android:name="android.appwidget.provider"
                android:resource="@xml/widget_provider" />
        </receiver>
    </application>
    <uses-sdk android:minSdkVersion="7" />
</manifest>
```
效果如图：
![img](P)  
![img](P)  