Android上的Widget使用了Java语言开发比W3C的Widget运行效率提高了不少，可以做更多的事情调用系统的API，除了UI上的限制外，我们可以考虑帮助系统完善一些appWidget，Android123给出大家一个开发Widget的模板。
```  
public class cwjWidget extends AppWidgetProvider {
	@Override
	public void onUpdate(Context context, AppWidgetManager appWidgetManager,
			int[] appWidgetIds) {
		context.startService(new Intent(context, UpdateService.class)); // 这里创建一个服务，防止出现等待超时对话框
	}
	public static class UpdateService extends Service { // 这个内部的服务我们推荐新开一个线程操作一些容易阻塞的情况，比如网络下载等等
		@Override
		public void onStart(Intent intent, int startId) {
			RemoteViews updateViews = buildUpdate(this);
			ComponentName thisWidget = new ComponentName(this, cwjWidget.class);
			AppWidgetManager manager = AppWidgetManager.getInstance(this);
			manager.updateAppWidget(thisWidget, updateViews);
		}
		public RemoteViews buildUpdate(Context context) {
			Resources res = context.getResources();
			RemoteViews updateViews = new RemoteViews(context.getPackageName(),
					R.layout.main); // 主Widget的layout布局
			PendingIntent pendingIntent = PendingIntent
					.getActivity(
							context,
							0 /* no requestCode */,
							new Intent(
									android.provider.Settings.ACTION_DEVICE_INFO_SETTINGS),
							0 /* no flags */);
			updateViews.setOnClickPendingIntent(R.id.ui, pendingIntent); // 单击view打开intent，目标为系统信息，就是上面的action位置
			updateViews.setTextViewText(R.id.info,
					android.os.Build.VERSION.CODENAME + "
							+ android.os.Build.ID); // 这里是API的获取系统版本的方法
			updateViews.setTextViewText(R.id.changelist,
					android.os.Build.FINGERPRINT);
			return updateViews;
		}
		@Override
		public IBinder onBind(Intent intent) {
			return null;
		}
	}
}
```
有关涉及到的 androidmanifest.xml内容
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.android123.widget"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="3" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <receiver
            android:name=".BuildWidget"
            android:label="android123_cwj" >
            <intent-filter>
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
            </intent-filter>
            <meta-data
                android:name="android.appwidget.provider"
                android:resource="@xml/widget" />
        </receiver>
        <service android:name=".cwjWidget$UpdateService" />
    </application>
</manifest>
```
androidmanifest.xml上面提到的  \res\xml\widget.xml文件内容
```  
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:initialLayout="@layout/widget"
    android:minHeight="72dip"
    android:minWidth="150dip"
    android:updatePeriodMillis="0" />
```
有关 main.xml的内容为
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/ui"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="6dip" >
    <TextView
        android:id="@+id/info"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="left"
        android:textSize="18dip" />
    <TextView
        android:id="@+id/changelist"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="4dip"
        android:gravity="left"
        android:textSize="9dip" />
</LinearLayout>
```