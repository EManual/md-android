本篇完成项目名称为：【心情记录器】
可将你的心情讯录并用桌面组件的形式展示于桌面上，并有丰富的表情可供选择并加载在桌面上，此功能类似于QQ上的各性签名，虽然手机是自己的但我们用的是Android手机，难免会有朋友拿来把玩，此时可以看到手机主人的心情状况不是很好吗？或许可以自己把一些不满的想法偷偷用心情记录下来也可以。注：此功能并不提供多个心情保存，只能保存一个，如果需要的朋友可以在后文为我提建议，当然我觉得多个心情保存个人不想要这个功能。。如果要的话请留言。
Tip:这个小东西完全 是App widget 桌面组件，所以必须通过长按桌面或者点击menu调出来。
上篇app Widget的DEMO只是为TextView添加点击事件，本篇将换另外的做法。通过点击布局弹出一个Activity的操作界面，之后在这个操作界面进行表情的选择和心情的保存，那么如何通过点击打开一个Activity 界面呢？
方法一：
在我们组件的updateAppWidget 中注册一个广播，为 TextView 添加 一个点击的广播，之后在onReceive  接收广播 中如下代码：
```  
Intent intn=new Intent(context, update.class);
intn.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
context.startActivity(intn); 
```
通过为Intent重新设置一个新的任务来打开Activity此法即可启动一个Activity，当然这种做法我是不建议的，因为重复了一个动作，具体怎么重复下文会具体告知大家。为TextView 注册广播可参考上面给出的链接，上文有介绍，在此就不多做介绍。
方法二：
此方法正是想告知大家如何重复的，即然我们可以为其注册广播那我们为什么不索性就为其做广播点击跳转？参考代码如下：
```  
Intent intentClick = new Intent(context, update.class);
PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intentClick, 0);
rv.setOnClickPendingIntent(R.id.layout, pendingIntent); 
```
通过这里的设置，上文将不用再去接收广播也可以达到想要的效果。
下面是通过点击打开 Activity 操作界面的效果图，在这里有点不好意思啦，因为即兴画的很丑，大家就将就着看吧，主要是理解App widget 的写法
如何通过点击保存的与app Widget 做动态交互呢？来看下面这段代码
```  
RemoteViews views = new RemoteViews(update.this
		.getPackageName(), R.layout.main);
views.setTextViewText(R.id.TextView01, text);
views.setImageViewResource(R.id.ImageView01, util.image[index]);
ComponentName widget = new ComponentName(update.this,
		widgetProvider.class);
AppWidgetManager manager = AppWidgetManager
		.getInstance(update.this);
manager.updateAppWidget(widget, views);
```
这里同样还是用到发RemoteViews 来接收值的变化，然后通过AppWidgetManager 这个桌面组件管理器去改新RemoteViews 。由于我们要时时刻保存用户记录的数据，这里只是用到了键值对保存。
下面贴出代码：
```  
import android.app.Activity;
import android.appwidget.AppWidgetManager;
import android.content.ComponentName;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.RemoteViews;
import android.widget.Spinner;
import android.widget.AdapterView.OnItemSelectedListener;
public class update extends Activity {
	private EditText mEditText;
	private Button mButton;
	private Spinner mSpinner;
	private int index = 0;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.update);
		mEditText = (EditText) findViewById(R.id.EditText01);
		mButton = (Button) findViewById(R.id.Button01);
		mSpinner = (Spinner) findViewById(R.id.Spinner01);
		final ImageView iv = (ImageView) findViewById(R.id.ImageView01);
		ArrayAdapter<String> adpter = new ArrayAdapter<String>(this,
				android.R.layout.simple_spinner_dropdown_item, util.text);
		adpter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
		mSpinner.setAdapter(adpter);
		SharedPreferences settings = getSharedPreferences("settinginfo",
				Activity.MODE_PRIVATE);
		index = settings.getInt("imageState", 0);
		mEditText.setText(settings.getString("heart", ""));
		iv.setImageResource(util.image[index]);
		mSpinner.setSelection(index);
		mSpinner.setOnItemSelectedListener(new OnItemSelectedListener() {
			@Override
			public void onItemSelected(AdapterView<?> arg0, View arg1,
					int arg2, long arg3) {
				index = arg2;
				iv.setImageResource(util.image[index]);
			}
			@Override
			public void onNothingSelected(AdapterView<?> arg0) {
			}
		});
		mButton.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				String text = mEditText.getText().toString();
				if (text.equals("")) {
					return;
				}
				SharedPreferences shared = getSharedPreferences("settinginfo",
						Activity.MODE_PRIVATE);
				SharedPreferences.Editor editor = shared.edit();
				editor.putInt("imageState", index);
				editor.putString("heart", text);
				editor.commit();
				RemoteViews views = new RemoteViews(update.this
						.getPackageName(), R.layout.main);
				views.setTextViewText(R.id.TextView01, text);
				views.setImageViewResource(R.id.ImageView01, util.image[index]);
				ComponentName widget = new ComponentName(update.this,
						widgetProvider.class);
				AppWidgetManager manager = AppWidgetManager
						.getInstance(update.this);
				manager.updateAppWidget(widget, views);
				update.this.finish();
			}
		});
	}
}
```
由于组件每创建一次都调用了一次updateAppWidget 这个方法，故此方法也必须去获取键值对
```  
import android.app.Activity;
import android.app.PendingIntent;
import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.SharedPreferences;
import android.widget.RemoteViews;
import android.widget.Toast;
public class widgetProvider extends AppWidgetProvider {
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
	}
	public static void updateAppWidget(Context context,
			AppWidgetManager appWidgeManger, int appWidgetId) {
		rv = new RemoteViews(context.getPackageName(), R.layout.main);
		SharedPreferences shared = context.getSharedPreferences("settinginfo",
				Activity.MODE_PRIVATE);
		// util.index = settings.getInt("imageState", 0);
		// mEditText.setText(settings.getString("heart", ""));
		rv.setTextViewText(
				R.id.TextView01,
				shared.getString("heart",
						context.getResources().getString(R.string.load)));
		rv.setImageViewResource(R.id.ImageView01,
				util.image[shared.getInt("imageState", 0)]);

		Intent intentClick = new Intent(context, update.class);
		PendingIntent pendingIntent = PendingIntent.getActivity(context, 0,
				intentClick, 0);
		rv.setOnClickPendingIntent(R.id.layout, pendingIntent);
		appWidgeManger.updateAppWidget(appWidgetId, rv);
	}
}
```