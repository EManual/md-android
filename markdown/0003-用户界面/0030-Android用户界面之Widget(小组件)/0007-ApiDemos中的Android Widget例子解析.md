我们的Android Widget开发之旅开始啦，看看Android SDK中ApiDemos上的Widget例子，下面的代码分为3个文件可以清楚的看到整个框架，主要是AppWidgetProvider类中的onUpdate、onDeleted、 onEnabled、onDisabled和updateAppWidget方法之间的状态改变，使用Logcat仔细分析一个widget的生命周期。
涉及到的文件有
```  
ExampleAppWidgetConfigure.java
ExampleBroadcastReceiver.java
res/layout/appwidget_configure.xml
res/layout/appwidget_provider.xml
res/xml/appwidget_provider.xml
```
代码如下：
```  
public class ExampleAppWidgetProvider extends AppWidgetProvider {
	private static final String TAG = "ExampleAppWidgetProvider"; // logcat调试信息
	public void onUpdate(Context context, AppWidgetManager appWidgetManager,
			int[] appWidgetIds) {
		Log.d(TAG, "onUpdate");
		// - 创建一个RemoteViews 对象
		// - 设置RemoteViews 对象的文本
		// - 告诉AppWidgetManager 显示 views对象给widget.
		final int N = appWidgetIds.length;
		for (int i = 0; i < N; i++) {
			int appWidgetId = appWidgetIds;
			String titlePrefix = ExampleAppWidgetConfigure.loadTitlePref(
					context, appWidgetId);
			updateAppWidget(context, appWidgetManager, appWidgetId, titlePrefix);
		}
	}
	public void onDeleted(Context context, int[] appWidgetIds) {
		Log.d(TAG, "onDeleted");
		// 当用户删除Widget时触发该
		final int N = appWidgetIds.length;
		for (int i = 0; i < N; i++) {
			ExampleAppWidgetConfigure.deleteTitlePref(context, appWidgetIds);
		}
	}
	public void onEnabled(Context context) {
		Log.d(TAG, "onEnabled");
		// 当widget创建时注册TIMEZONE_CHANGED 和 TIME_CHANGED改变的广播获取这些时间和区域的改变事件
		PackageManager pm = context.getPackageManager();
		pm.setComponentEnabledSetting(new ComponentName(
				"com.example.android.apis",
				".appwidget.ExampleBroadcastReceiver"),
				PackageManager.COMPONENT_ENABLED_STATE_ENABLED,
				PackageManager.DONT_KILL_APP);
	}
	public void onDisabled(Context context) {
		Log.d(TAG, "onDisabled");
		Class clazz = ExampleBroadcastReceiver.class;
		PackageManager pm = context.getPackageManager();
		pm.setComponentEnabledSetting(new ComponentName(
				"com.example.android.apis",
				".appwidget.ExampleBroadcastReceiver"),
				PackageManager.COMPONENT_ENABLED_STATE_ENABLED,
				PackageManager.DONT_KILL_APP);
	}
	static void updateAppWidget(Context context,
			AppWidgetManager appWidgetManager, int appWidgetId,
			String titlePrefix) {
		Log.d(TAG, "updateAppWidget appWidgetId=" + appWidgetId
				+ " titlePrefix=" + titlePrefix);
		CharSequence text = context.getString(R.string.appwidget_text_format,
				ExampleAppWidgetConfigure.loadTitlePref(context, appWidgetId),
				"0x" + Long.toHexString(SystemClock.elapsedRealtime()));
		RemoteViews views = new RemoteViews(context.getPackageName(),
				R.layout.appwidget_provider);
		views.setTextViewText(R.id.appwidget_text, text);
		appWidgetManager.updateAppWidget(appWidgetId, views);
	}
}

import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.BroadcastReceiver;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.os.SystemClock;
import android.util.Log;
import android.widget.RemoteViews;
import java.util.ArrayList;
import com.example.android.apis.R;
public class ExampleBroadcastReceiver extends BroadcastReceiver {
	public void onReceive(Context context, Intent intent) {
		Log.d("ExmampleBroadcastReceiver", "intent=" + intent);
		// For our example, we'll also update all of the widgets when the
		// timezone
		// changes, or the user or network sets the time.
		String action = intent.getAction();
		if (action.equals(Intent.ACTION_TIMEZONE_CHANGED)
				|| action.equals(Intent.ACTION_TIME_CHANGED)) {
			AppWidgetManager gm = AppWidgetManager.getInstance(context);
			ArrayList<Integer> appWidgetIds = new ArrayList();
			ArrayList<String> texts = new ArrayList();
			ExampleAppWidgetConfigure.loadAllTitlePrefs(context, appWidgetIds, texts);
			final int N = appWidgetIds.size();
			for (int i = 0; i < N; i++) {
				ExampleAppWidgetProvider.updateAppWidget(context, gm,
						appWidgetIds.get(i), texts.get(i));
			}
		}
	}
}
import android.app.Activity;
import android.appwidget.AppWidgetManager;
import android.content.Context;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.EditText;
import java.util.ArrayList;
public class ExampleAppWidgetConfigure extends Activity {
	static final String TAG = "ExampleAppWidgetConfigure";
	private static final String PREFS_NAME = "com.example.android.apis.appwidget.ExampleAppWidgetProvider";
	private static final String PREF_PREFIX_KEY = "prefix_";
	int mAppWidgetId = AppWidgetManager.INVALID_APPWIDGET_ID;
	EditText mAppWidgetPrefix;
	public ExampleAppWidgetConfigure() {
		super();
	}
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setResult(RESULT_CANCELED);
		setContentView(R.layout.appwidget_configure);
		mAppWidgetPrefix = (EditText) findViewById(R.id.appwidget_prefix);
		findViewById(R.id.save_button).setOnClickListener(mOnClickListener);
		Intent intent = getIntent();
		Bundle extras = intent.getExtras();
		if (extras != null) {
			mAppWidgetId = extras.getInt(AppWidgetManager.EXTRA_APPWIDGET_ID,
					AppWidgetManager.INVALID_APPWIDGET_ID);
		}
		if (mAppWidgetId == AppWidgetManager.INVALID_APPWIDGET_ID) {
			finish();
		}
		mAppWidgetPrefix.setText(loadTitlePref(ExampleAppWidgetConfigure.this,
				mAppWidgetId));
	}
	View.OnClickListener mOnClickListener = new View.OnClickListener() {
		public void onClick(View v) {
			final Context context = ExampleAppWidgetConfigure.this;
			String titlePrefix = mAppWidgetPrefix.getText().toString();
			saveTitlePref(context, mAppWidgetId, titlePrefix);
			AppWidgetManager appWidgetManager = AppWidgetManager
					.getInstance(context);
			ExampleAppWidgetProvider.updateAppWidget(context, appWidgetManager,
					mAppWidgetId, titlePrefix);

			Intent resultValue = new Intent();
			resultValue.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID,
					mAppWidgetId);
			setResult(RESULT_OK, resultValue);
			finish();
		}
	};
	static void saveTitlePref(Context context, int appWidgetId, String text) {
		SharedPreferences.Editor prefs = context.getSharedPreferences(
				PREFS_NAME, 0).edit();
		prefs.putString(PREF_PREFIX_KEY + appWidgetId, text);
		prefs.commit();
	}
	static String loadTitlePref(Context context, int appWidgetId) {
		SharedPreferences prefs = context.getSharedPreferences(PREFS_NAME, 0);
		String prefix = prefs.getString(PREF_PREFIX_KEY + appWidgetId, null);
		if (prefix != null) {
			return prefix;
		} else {
			return context.getString(R.string.appwidget_prefix_default);
		}
	}
	static void deleteTitlePref(Context context, int appWidgetId) {
	}
	static void loadAllTitlePrefs(Context context,
			ArrayList<Integer> appWidgetIds, ArrayList<String> texts) {
	}
}
```