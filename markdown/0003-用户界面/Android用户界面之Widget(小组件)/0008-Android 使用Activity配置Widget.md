在初始化widget时有时需要让用户输入一些数据，从而将输入的数据显示到widget中，这就需要Configuration Activity来和用户进行交互，并将用户输入的数据返回给Widget的host。
这个Activit在AndroidManifest文件中需要声明action包含，这样才能使host可以调用。例如：
```  
<activity android:name=".ExampleAppWidgetConfigure">
	<intent-filter>
		<action android:name="android.appwidget.action.APPWIDGET_CONFIGURE" />
	</intent-filter>
</activity>
```
另外，还需要在AppWidgetProviderInfo XML 文件中声明android:configure，使其指向该Activity。
```  
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android" ...
	android:configure="com.example.android.ExampleAppWidgetConfigure" ... >
</appwidget-provider>
```
其中在实现该Activity时需要注意两个地方：
1、Activity必须返回数据，同时数据中还需要包含widget的id，这个id保存在intent的Extra中，其键为EXTRA_APPWIDGET_ID。
2、当使用Activity来初始化Widget时，onUpdate将不会被调用，因为系统不会发送ACTION_APPWIDGET_UPDATE 的广播。（这一点值得怀疑，因为我在测试过程中发现，其实还是会调用这个函数）
另外在使用这种调用Activity获取数据的方式时，被调用的Activity需要考虑到用户还没有选择，就back的情况，所以在Activity的onCreate中加入setResult(RESULT_CANCELED)，从而保证正确处理用户在还没有确定就back的情况。
下面是一个具体的实现过程，分为五个步骤。
当一个App Widget使用一个配置活动,那么当配置结束时,就应该由这个活动来更新这个App Widget.你可以直接AppWidgetManager里请求一个更新来这么做.
下面是恰当的更新App Widget 以及关闭配置活动这个过程的一个概要描述:
1.首先，从启动这个活动的意图中获取App Widget ID： 
```  
Intent intent = getIntent();
Bundle extras = intent.getExtras();
if (extras != null) {
	mAppWidgetId = extras.getInt(
	AppWidgetManager.EXTRA_APPWIDGET_ID,
	AppWidgetManager.INVALID_APPWIDGET_ID);
}
```
2.实施你的App Widget 配置。
3.当配置完成后，通过调用getInstance(Context)获取一个AppWidgetManager实例: 
```  
AppWidgetManager appWidgetManager = AppWidgetManager.getInstance(context);
```
4.以一个RemoteViews布局调用updateAppWidget(int, RemoteViews)更新App Widget: 
```  
RemoteViews views = new RemoteViews(context.getPackageName(), R.layout.example_appwidget);
appWidgetManager.updateAppWidget(mAppWidgetId, views);
```
5.最后，创建返回意图，设置活动结果，并结束这个活动:
```  
Intent resultValue = new Intent();
resultValue.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID, mAppWidgetId);
setResult(RESULT_OK, resultValue);
finish();
```