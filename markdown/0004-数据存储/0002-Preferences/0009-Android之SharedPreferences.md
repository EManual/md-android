SharedPreferences是Android平台上一个轻量级的存储类，主要是保存一些常用的配置比如窗口状态，比如我们可以通过SharedPreferences来判断程序是不是第一次运行。
下面的实例将用于介绍怎样通过SharedPreferences来判断程序是否是第一次运行，其实现思路很简单，通过在SharedPreferences中存储键值表示程序是否第一次运行。代码如下：
```  
public class PreferenceTestMain extends Activity {
	public static final String PREFS_NAME = "MyPrefsFile";
	public static final String FIRST_RUN = "first";
	private boolean first;
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// Restore preferences
		SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
		first = settings.getBoolean(FIRST_RUN, true);
		if (first) {
			Toast.makeText(this, "The Application is first run",
					Toast.LENGTH_LONG).show();
		} else {
			Toast.makeText(this, "The Application is not first run",
					Toast.LENGTH_LONG).show();
		}
	}
	@Override
	protected void onStop() {
		super.onStop();
		// We need an Editor object to make preference changes.
		// All objects are from android.context.Context
		SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
		SharedPreferences.Editor editor = settings.edit();
		if (first) {
			editor.putBoolean(FIRST_RUN, false);
		}
		// Commit the edits!
		editor.commit();
	}
}
```
其中在onCreate方法中读取
SharedPreferences信息，在onStop中保存
SharedPreferences信息。注意程序的状态信息一般都在
onStop保存。
程序截图如下：
![img](P)  
![img](P)  
如果我们通过应用程序管理界面将其数据清除掉后，我们再次运行时发现程序又恢复到第一次运行的状态：
![img](P)  
