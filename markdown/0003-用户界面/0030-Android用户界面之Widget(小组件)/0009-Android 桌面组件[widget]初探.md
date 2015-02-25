下面是本篇的大纲：
```  
1、AppWidget 框架类
2、在 Android 如何使用 Widget
3、AppWidget 框架的主要类介绍
4、DEMO 讲解
```
#### 1、AppWidget 框架类
1、AppWidgetProvider：继承自BroadcastRecevier，在AppWidget应用update、enable、disable和delete时接收通知。其中，onUpdate、onReceive 是最常用到的方法，它们接收更新通知。
2、 AppWidgetProvderInfo：描述 AppWidget 的大小、更新频率和初始界面等信息，以XML 文件形式存在于应用的 res/xml/目录下。
3、AppWidgetManger ：负责管理 AppWidget ，向 AppwidgetProvider 发送通知。
4、RemoteViews ：一个可以在其他应用进程中运行的类，向 AppWidgetProvider 发送通知。 
#### 2、在 Android 如何使用 Widget 
1、长按主界面
2、之后弹出一个对话框，里面就有android 内置的一些桌面组件
#### 3、AppWidget 框架的主要类介绍 
1) AppWidgetManger 类
bindAppWidgetId(int appWidgetId, ComponentName provider)
通过给定的ComponentName 绑定appWidgetId
getAppWidgetIds(ComponentName provider)
通过给定的ComponentName 获取AppWidgetId
getAppWidgetInfo(int appWidgetId)
通过AppWidgetId 获取 AppWidget 信息
getInstalledProviders()
返回一个List<AppWidgetProviderInfo>的信息
getInstance(Context context)
获取 AppWidgetManger 实例使用的上下文对象
updateAppWidget(int[] appWidgetIds, RemoteViews views)
通过appWidgetId 对传进来的 RemoteView 进行修改，并重新刷新AppWidget 组件
updateAppWidget(ComponentName provider, RemoteViews views)
通过 ComponentName 对传进来的 RemoeteView 进行修改，并重新刷新AppWidget 组件
updateAppWidget(int appWidgetId, RemoteViews views)
通过appWidgetId 对传进来的 RemoteView 进行修改，并重新刷新AppWidget 组件
2) 继承自 AppWidgetProvider 可实现的方法为如下：
1、onDeleted(Context context, int[] appWidgetIds)
2、onDisabled(Context context)
3、onEnabled(Context context)
4、onReceive(Context context, Intent intent)
Tip:因为 AppWidgetProvider 是继承自BroadcastReceiver  所以可以重写onRecevie 方法，当然必须在后台注册Receiver
5、onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) 
#### 4、Demo讲解
下面是我今天做的一个实例，提供给大家练习时做参考，效果如下：在布局中放一个 TextView 做桌面组件，然后设置TextView 的 Clickable="true" 使其有点击的功能，然后我们点击它时改变它的字体，再点击时变回来，详细操作如下流程：
1、新建AppWidgetProvderInfo
2、写一个类继承自AppWidgetProvider
3、后台注册Receiver
4、使 AppWidget 组件支持点击事件
5、如何使TextView 在两种文本间来回跳转 
问题抛出来了，那么一起解决它吧。
1、新建AppWidgetProvderInfo
代码如下：
```  
<?xml version="1.0" encoding="UTF-8"?>
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="60dp"
    android:minHeight="30dp"
    android:updatePeriodMillis="86400000"
    android:initialLayout="@layout/main">
</appwidget-provider> 
```
Tip:上文说过AppWidgetProvderInfo 是在res/xml 的文件形式存在的，看参数不难理解，比较重要的是这里android:initialLayout="@layout/main" 此句为指定桌面组件的布局文件。
2、写一个类继承自AppWidgetProvider
主要代码如下：
```  
public class widgetProvider extends AppWidgetProvider "
```
并重写两个方法
```  
@Override
public void onUpdate(Context context, AppWidgetManager appWidgetManager,
	int[] appWidgetIds) {}
@Override
public void onReceive(Context context, Intent intent) {} 
```
Tip：onUpdate 为组件在桌面上生成时调用，并更新组件UI，onReceiver 为接收广播时调用更新UI，一般这两个方法是比较常用的。
3、后台注册Receiver
后台配置文件代码如下：
```  
<receiver android:name=".widgetProvider">
	<meta-data android:name="android.appwidget.provider"
		android:resource="@xml/appwidget_provider"></meta-data>
	<intent-filter>
		<action android:name="com.terry.action.widget.click"></action>
		<action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
	</intent-filter>
</receiver> 
```
Tip:因为是桌面组件，所以暂时不考虑使用Activity界面，当然你在实现做项目时可能会需要点击时跳转到Activity应用程序上做操作，典型的案例为Android  提供的音乐播放器。上面代码中比较重要的是这一句
```  
<meta-data android:name="android.appwidget.provider"  			
		   android:resource="@xml/appwidget_provider">
</meta-data>
```
大意为指定桌面应用程序的AppWidgetProvderInfo 文件，使其可作其管理文件。
4、使 AppWidget 组件支持点击事件
先看代码：
```  
public static void updateAppWidget(Context context,
		AppWidgetManager appWidgeManger, int appWidgetId) {
	rv = new RemoteViews(context.getPackageName(), R.layout.main);
	Intent intentClick = new Intent(CLICK_NAME_ACTION);
	PendingIntent pendingIntent = PendingIntent.getBroadcast(context, 0,
			intentClick, 0);
	rv.setOnClickPendingIntent(R.id.TextView01, pendingIntent);
	appWidgeManger.updateAppWidget(appWidgetId, rv);
}
```
此方法为创建组件时onUpdate调用的更新UI的方法，代码中使用RemoteView找到组件的布局文件，同时为其设置广播接收器CLICK_NAME_ACTION并且通过RemoteView 的setOnClickPendingIntent 方法找到我想触发事件的TextView 为其设置广播。接着
```  
@Override
public void onReceive(Context context, Intent intent) {
	super.onReceive(context, intent);
	if (rv == null) {
		rv = new RemoteViews(context.getPackageName(), R.layout.main);
	}
	if (intent.getAction().equals(CLICK_NAME_ACTION)) {
		if (uitil.isChange) {
			rv.setTextViewText(R.id.TextView01, context.getResources().getString(R.string.load));
		} else {
			rv.setTextViewText(R.id.TextView01, context.getResources().getString(R.string.change));
		}
		Toast.makeText(context, Boolean.toString(uitil.isChange),
				Toast.LENGTH_LONG).show();
		uitil.isChange = !uitil.isChange;
	}
	AppWidgetManager appWidgetManger = AppWidgetManager.getInstance(context);
	int[] appIds = appWidgetManger.getAppWidgetIds(new ComponentName(
			context, widgetProvider.class));
	appWidgetManger.updateAppWidget(appIds, rv);
} 
```
在onReceiver 中通过判断传进来的广播来触发动作。
5、如何使TextView 在两种文本间来回跳转
如何TextView在来两种状态中来回呢？这也是我比较调试最久的一个难点，问题出在对AppWidget的理解不够深入。如果我的设想没错的话AppWidget的生命周期应该在每接收一次广播执行一次为一个生命周期结束，也就是说你在重写的AppWidgetProvider类里面声明全局变量做状态判断，每次状态改变AppWidgetProvider再接收第二次广播时即为你重新初始化也就是说桌件为你重新实例化了一次AppWidgetProvider。今天我因为在里面放了一个boolean值初始化为true，观察调试看到每次进入都为TRUE故你在设置桌面组件时，全局变量把它声明在另外一个实体类用来判断是没问题的，切忌放在本类。代码参考onReceiver方法。
代码：
```  
import android.app.PendingIntent;
import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.widget.RemoteViews;
import android.widget.Toast;
public class widgetProvider extends AppWidgetProvider {
	private static final String CLICK_NAME_ACTION = "com.terry.action.widget.click";
	private static RemoteViews rv;
	@Override
	public void onUpdate(Context context, AppWidgetManager appWidgetManager,
			int[] appWidgetIds) {
		final int N = appWidgetIds.length;
		for (int i = 0; i < N; i++) {
			int appWidgetId = appWidgetIds;
			updateAppWidget(context, appWidgetManager, appWidgetId);
		}
	}
	@Override
	public void onReceive(Context context, Intent intent) {
		super.onReceive(context, intent);
		if (rv == null) {
			rv = new RemoteViews(context.getPackageName(), R.layout.main);
		}
		if (intent.getAction().equals(CLICK_NAME_ACTION)) {
			if (uitil.isChange) {
				rv.setTextViewText(R.id.TextView01, context.getResources()
						.getString(R.string.load));
			} else {
				rv.setTextViewText(R.id.TextView01, context.getResources()
						.getString(R.string.change));

			}
			Toast.makeText(context, Boolean.toString(uitil.isChange),
					Toast.LENGTH_LONG).show();
			uitil.isChange = !uitil.isChange;

		}
		AppWidgetManager appWidgetManger = AppWidgetManager
				.getInstance(context);
		int[] appIds = appWidgetManger.getAppWidgetIds(new ComponentName(
				context, widgetProvider.class));
		appWidgetManger.updateAppWidget(appIds, rv);
	}
	public static void updateAppWidget(Context context,
			AppWidgetManager appWidgeManger, int appWidgetId) {
		rv = new RemoteViews(context.getPackageName(), R.layout.main);
		Intent intentClick = new Intent(CLICK_NAME_ACTION);
		PendingIntent pendingIntent = PendingIntent.getBroadcast(context, 0,
				intentClick, 0);
		rv.setOnClickPendingIntent(R.id.TextView01, pendingIntent);
		appWidgeManger.updateAppWidget(appWidgetId, rv);
	}
}
```