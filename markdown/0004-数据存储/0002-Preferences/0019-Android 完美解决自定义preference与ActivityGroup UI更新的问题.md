之前发过一篇有关于自定义preference在ActivityGroup的包容下出现UI不能更新的问题，当时还以为是Android的一个BUG现在想想真可笑。其实是自己对机制的理解不够深刻，看来以后要多看看源码才行。
本篇讲述内容大致为如何自定义preference 开始到与ActivityGroup 互用下UI更新的解决方法。
首先从扩展preference开始：
类文件必须继承自Preference并实现构造函数，这里我一般实现两个构造函数分别如下（类名为：test）：
```  
public test(Context context) {
	this(context, null);
}
public test(Context context, AttributeSet attrs) {
	super(context, attrs);
}
```
这里第二个构造函数第二个参数为可以使用attrs 为我们自定义的preference 添加扩展的注册属性，比如我们如果希望为扩展的preference 添加一个数组引用，就可使用如下代码：
```  
int resouceId = attrs.getAttributeResourceValue(null, "Entries", 0);
if (resouceId > 0) {
	mEntries = getContext().getResources().getTextArray(resouceId);
}
```
这里的mEntries 是头部声明的一个数组，我们可以在xml文件通过 Entries=数组索引得到一个数组。在这里不深入为大家示范了。
我们扩展preference 有时想让其UI更丰富更好看，这里我们可以通过引用一个layout 文件为其指定UI，可以通过实现如下两个回调函数：
```  
@Override
protected View onCreateView(ViewGroup parent) {
	return LayoutInflater.from(getContext()).inflate(
			R.layout.preference_screen, parent, false);
}
```
此回调函数与onBindView 一一对应，并优先执行于onBindView ，当创建完后将得到的VIEW返回出去给onBindView处理，如下代码：
```  
@Override
protected void onBindView(View view) {
	super.onBindView(view);
	canlendar = Calendar.getInstance();
	layout = (RelativeLayout) view.findViewById(R.id.area);
	title = (TextView) view.findViewById(R.id.title);
	summary = (TextView) view.findViewById(R.id.summary);
	layout.setOnClickListener(this);
	title.setText(getTitle());
	summary.setText(getPersistedString(canlendar.get(Calendar.YEAR) + "/
			+ (canlendar.get(Calendar.MONTH) + 1) + "/
			+ canlendar.get(Calendar.DAY_OF_MONTH)));
}
```
Tip：onBindView不是必须的，可以将onBindView里的处理代码在onCreateView回调函数一并完成然后返回给onBindView，具体怎么写看自己的代码风格吧。我个人比较喜欢这种写法，比较明了。
下面我们来了解一下我扩展preference 比较常用到的几个方法：
```  
compareTo(Preference another)
与另外一个preference比较，如果相等则返回0，不相等则返回小于0的数字。
getContext()
获取上下文
getEditor()
得到一个SharePrefence 的Editor 对象
getIntent()
获取Intetn
getKey()
获取当前我们在XML为其注册的KEY
getLayoutResource()
得到当前layout 的来源
getOnPreferenceChangeListener()
值改变的监听事件
getPreferenceManager()
获得一个preference管理
getSharedPreferences()
通过获得管理获取当前的sharePreferences
getSummary()
获得当前我们在XML为其注册的备注为summary 的值。Tip：在OnBindView 或者onCreateView 找到VIEW的时候如果存在summary 的View 对象必须为其设置summary
getTitle()
如上，不过这里是获取标题
```
callChangeListener(Object newValue)
如果你希望你扩展的Preference 可以支持当数值改变时候可以调用OnPreferenceChangeListener此监听方法，则必须调用此方法，查看该方法源码为：
```  
protected boolean callChangeListener(Object newValue) {
	return mOnChangeListener == null ? true : mOnChangeListener.onPreferenceChange(this, newValue);
}
``` 
源码简单不做过多介绍，只是实现一个接口。
getPersistedBoolean(boolean defaultReturnValue)
获得一个保存后的布尔值，查看一下源码：
```  
protected boolean getPersistedBoolean(boolean defaultReturnValue) {
	if (!shouldPersist()) {
		return defaultReturnValue;
	}
	return mPreferenceManager.getSharedPreferences().getBoolean(mKey, defaultReturnValue);
}
```
如果你有接触过sharePreference 相信一眼就能看出这里它为我们做了什么。
getPersistedFloat(float defaultReturnValue)
如上，这里获取一个Float 的值
getPersistedInt(int defaultReturnValue)
如上，获取一个int 的值
getPersistedLong(long defaultReturnValue)
如上，获取一个Long 型数值
getPersistedString(String defaultReturnValue)
如上，获取一个String 数值
persistBoolean(boolean value)
将一个布尔值保存在sharepreference中，查看一下源码：
```  
protected boolean persistBoolean(boolean value) {
	if (shouldPersist()) {
		if (value == getPersistedBoolean(!value)) {
			// It's already there, so the same as persisting
			return true;
		}
		
		SharedPreferences.Editor editor = mPreferenceManager.getEditor();
		editor.putBoolean(mKey, value);
		tryCommit(editor);
		return true;
	}
	return false;
}
```
都是sharePreference 的知识，这里不做过多介绍。其他的跟上面的都 一样，略过。
通过如上的一些设置，一个基本的扩展preference就己经完成，下面来讲讲如果在ActivityGroup里面让扩展的preference可以更新UI。之前农民伯伯探讨过，他建议我使用onContentChanged()方法，可以使UI更新，试了一下发现有些许问题，不过非常感谢农民伯伯。这个方法是全局刷新，则全部UI都刷新一次，但是这样不是很合理，我想了一下，那既然此方法可以更新UI那么一定可以行得通，我查看一下源码，下面把源码贴出来：
```  
@Override
public void onContentChanged() {
	super.onContentChanged();
	postBindPreferences();
}
[Tags]/**
 [Tags]* Posts a message to bind the preferences to the list view.
 [Tags]* <p>
 [Tags]* Binding late is preferred as any custom preference types created in
 [Tags]* {@link onCreate(Bundle)} are able to have their views recycled.
 [Tags]*/
private void postBindPreferences() {
	if (mHandler.hasMessages(MSG_BIND_PREFERENCES)) return;
	mHandler.obtainMessage(MSG_BIND_PREFERENCES).sendToTarget();
}
private void bindPreferences() {
	final PreferenceScreen preferenceScreen = getPreferenceScreen();
	if (preferenceScreen != null) {
		preferenceScreen.bind(getListView());
	}
}
private static final int MSG_BIND_PREFERENCES = 0;
private Handler mHandler = new Handler() {
	@Override
	public void handleMessage(Message msg) {
		switch (msg.what) {
			
			case MSG_BIND_PREFERENCES:
				bindPreferences();
				break;
		}
	}
};
```
原来，这里它是另开一条线程来更新UI，然后当值发生变化时为其发送消息，在消息队列里面处理UI，只不过它这里继承了listActivity 更新了一整个listView ，那么我们就将它提取出来，只更新我们想要的UI则可。OK，思路出来了，下面将我扩展的一个preference 的源码提供出来：
```  
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import android.R;
import android.app.Dialog;
import android.content.Context;
import android.content.DialogInterface;
import android.os.Handler;
import android.os.Message;
import android.preference.Preference;
import android.preference.PreferenceGroup;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.ListView;
import android.widget.RelativeLayout;
import android.widget.SimpleAdapter;
import android.widget.TextView;
public class PreferenceScreenExt extends PreferenceGroup implements
		OnItemClickListener, DialogInterface.OnDismissListener {
	private Dialog dialog;
	private TextView title, summary;
	private RelativeLayout area;
	private ListView listView;
	List<Preference> list;
	private List<HashMap<String, String>> listStr;
	private CharSequence[] mEntries;
	private String mValue;
	private SimpleAdapter simple;
	private static final int MSG_BIND_PREFERENCES = 0;
	private Handler mHandler = new Handler() {
		@Override
		public void handleMessage(Message msg) {
			switch (msg.what) {

			case MSG_BIND_PREFERENCES:
				setValue(mValue);
				break;
			}
		}
	};
	public PreferenceScreenExt(Context context, AttributeSet attrs) {
		this(context, attrs, android.R.attr.preferenceScreenStyle);
	}
	public PreferenceScreenExt(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, android.R.attr.preferenceScreenStyle);
		int resouceId = attrs.getAttributeResourceValue(null, "Entries", 0);
		if (resouceId > 0) {
			mEntries = getContext().getResources().getTextArray(resouceId);
		}
	}
	@Override
	protected void onBindView(View view) {
		// TODO Auto-generated method stub
		area = (RelativeLayout) view.findViewById(R.id.area);
		title = (TextView) view.findViewById(R.id.title);
		summary = (TextView) view.findViewById(R.id.summary);
		title.setText(getTitle());
		summary.setText(getPersistedString(getSummary().toString()));
		area.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showDialog();
			}
		});
	}
	@Override
	protected View onCreateView(ViewGroup parent) {
		View view = LayoutInflater.from(getContext()).inflate(
				R.layout.preference_screen, parent, false);
		return view;
	}
	public void bindView(ListView listview) {
		int length = mEntries.length;
		int i = 0;
		listStr = new ArrayList<HashMap<String, String>>();
		for (i = 0; i < length; i++) {
			HashMap<String, String> map = new HashMap<String, String>();
			map.put("keyname", mEntries[i].toString());
			listStr.add(map);
		}
		simple = new SimpleAdapter(getContext(), listStr, R.layout.dialog_view,
				new String[] { "keyname" }, new int[] { R.id.text });
		listview.setAdapter(simple);
		listview.setOnItemClickListener(this);
	}
	public void showDialog() {
		listView = new ListView(getContext());
		bindView(listView);
		dialog = new Dialog(getContext(), android.R.style.Theme_NoTitleBar);
		dialog.setContentView(listView);
		dialog.setOnDismissListener(this);
		dialog.show();
	}
	@Override
	public void onItemClick(AdapterView<?> parent, View view, int position,
			long id) {
		mValue = listStr.get(position).get("keyname").toString();
		persistString(mValue);
		callChangeListener(mValue);
		dialog.dismiss();
	}
	@Override
	public void onDismiss(DialogInterface dialog) {
	}
	private OnPreferenceChangeListener temp;
	public interface OnPreferenceChangeListener {
		public boolean onPreferenceChange(Preference preference, Object newValue);
	}
	public void setOnPreferenceChangeListener(
			OnPreferenceChangeListener preference) {
		this.temp = preference;
	}
	public void setValue(String value) {
		summary.setText(value);
	}
	public boolean callChangeListener(Object newValue) {
		return temp == null ? true : temp.onPreferenceChange(this, newValue);
	}
	public void postBindPreferences() {
		if (mHandler.hasMessages(MSG_BIND_PREFERENCES))
			return;
		mHandler.obtainMessage(MSG_BIND_PREFERENCES).sendToTarget();
	}
}
```
然后在preferenceActivity 界面在回调函数：onPreferenceChange调用postBindPreferences即可更新。
Tip:这里的onPreferenceChange  调用postBindPreferences 不是必须的，你同样可以在内部里面实现，通过执行某一操作发送消息也可。