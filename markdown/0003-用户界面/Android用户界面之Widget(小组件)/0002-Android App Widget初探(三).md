TestActivity类
```  
import android.app.PendingIntent;
import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.widget.RemoteViews;
[Tags]/**
 [Tags]* 为手机添加桌面控件，当点击桌面控件时则进入主程序
 [Tags]* AppWidgetProvider：继承自BroadcastRecevier，在AppWidget应用update、enable、
 [Tags]* disable和delete时接收通知。 其中，onUpdate、onReceive是最常用到的方法，它们接收更新通知
 [Tags]* 
 [Tags]*/
public class TestActivity extends AppWidgetProvider {
	[Tags]/**
	 [Tags]* 用来间隔的更新App
	 [Tags]* Widget，间隔时间用AppWidgetProviderInfo里的updatePeriodMillis属性定义(单位为毫秒)。
	 [Tags]* 注意：SDK1.5之后此android:updatePeriodMillis就失效了，要自己创建service更新。 这个方法也会在用户添加App
	 [Tags]* Widget时被调用，因此它应该执行基础的设置，比如为视图定义事件处理器并启动一个临时的服务Service，如果需要的话。
	 [Tags]* 但是，如果你已经声明了一个配置活动，这个方法在用户添加App Widget时将不会被调用，
	 [Tags]* 而只在后续更新时被调用。配置活动应该在配置完成时负责执行第一次更新。
	 [Tags]*/
	@Override
	public void onUpdate(Context context, AppWidgetManager appWidgetManager,
			int[] appWidgetIds) {
		System.out.println("onUpdate");
		// 点击桌面组件时进入主程序入口
		Intent intent = new Intent(context, MainActivity.class);
		PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);
		// RemoteViews类描述了一个View对象能够显示在其他进程中，可以融合layout资源文件实现布局。
		// 虽然该类在android.widget.RemoteViews而不是appWidget下面,但在Android
		// Widgets开发中会经常用到它，
		// 主要是可以跨进程调用(appWidget由一个服务宿主来统一运行的)。
		RemoteViews myRemoteViews = new RemoteViews(context.getPackageName(),
				R.layout.my_layout);
		// myRemoteViews.setImageViewResource(R.id.imageView,
		// R.drawable.png1);//设置布局控件的属性（要特别注意）
		myRemoteViews.setOnClickPendingIntent(R.id.btn, pendingIntent);
		ComponentName myComponentName = new ComponentName(context,
				TestActivity.class);
		// 负责管理AppWidget，向AppwidgetProvider发送通知。提供了更新AppWidget状态，获取已经安装的Appwidget提供信息和其他的相关状态
		AppWidgetManager myAppWidgetManager = AppWidgetManager
				.getInstance(context);
		myAppWidgetManager.updateAppWidget(myComponentName, myRemoteViews);
	}
	[Tags]/**
	 [Tags]* 当App Widget从宿主中删除时被调用。
	 [Tags]*/
	@Override
	public void onDeleted(Context context, int[] appWidgetIds) {
		System.out.println("onDeleted");
		super.onDeleted(context, appWidgetIds);
	}
	[Tags]/**
	 [Tags]* 当一个App Widget实例第一次创建时被调用。 比如，如果用户添加两个App Widget实例，只在第一次被调用。
	 [Tags]* 如果你需要打开一个新的数据库或者执行其他对于所有的App Widget实例只需要发生一次的设置， 那么这里是完成这个工作的好地方。
	 [Tags]*/
	@Override
	public void onEnabled(Context context) {
		System.out.println("onEnabled");
		super.onEnabled(context);
	}
	[Tags]/**
	 [Tags]* 当你的App
	 [Tags]* Widget的最后一个实例被从宿主中删除时被调用。你应该在onEnabled(Context)中做一些清理工作，比如删除一个临时的数据库
	 [Tags]*/
	@Override
	public void onDisabled(Context context) {
		System.out.println("onDisabled");
		super.onDisabled(context);
	}
	[Tags]/**
	 [Tags]* 接收到每个广播时都会被调用，而且在上面的回调函数之前。 你通常不需要实现这个方法，因为缺省的AppWidgetProvider实现过滤所有App
	 [Tags]* Widget广播并恰当的调用上述方法。 注意: 在Android 1.5中，有一个已知问题，onDeleted()方法在调用时不被调用。
	 [Tags]* 为了规避这个问题，你可以像Group post中描述的那样实现onReceive()来接收这个onDeleted()回调。
	 [Tags]*/
	@Override
	public void onReceive(Context context, Intent intent) {
		System.out.println("onReceive");
		super.onReceive(context, intent);
	}
}
```