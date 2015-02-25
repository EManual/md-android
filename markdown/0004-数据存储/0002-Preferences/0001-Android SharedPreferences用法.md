大家在Android开发中 一点有这样的需求 就是需要保存一下与该程序有关的属性设置的问题 比如：window xp 中 <假设系统盘为 C:/> 的位置为： C:\Program Files 那么在android中是怎样呢？ 那就是：SharedPreferences
既然它是用来保存数据的 那么一点下面问题：
1. 如何创建
2. 如何加入数据
3. 如何取出数据
因为 很多程序都有这个需要 所以自己把该功能集成并列出一些接口函数 以后用的话 会方便很多 这个类名为：SharedPreferencesHelper
#### 1. 以指定名字来创建
```  
SharedPreferences sp;
SharedPreferences.Editor editor;
Context context;
public SharedPreferencesHelper(Context c, String name) {
	context = c;
	sp = context.getSharedPreferences(name, 0);
	editor = sp.edit();
}
```
#### 2. 以键值的方式加入数据
```  
public void putValue(String key, String value) {
	editor = sp.edit();
	editor.putString(key, value);
	editor.commit();
}
```
#### 3. 以String Key 为索引来取出数据
```  
public String getValue(String key) {
	return sp.getString(key, null);
}
```
#### 4. 如何使用 SharedPreferencesHelper
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;
public class SharedPreferencesUsage extends Activity {
	public final static String COLUMN_NAME = "name";
	public final static String COLUMN_MOBILE = "mobile";
	SharedPreferencesHelper sp;
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
		sp = new SharedPreferencesHelper(this, "contacts");
		sp.putValue(COLUMN_NAME, "Gryphone");
		sp.putValue(COLUMN_MOBILE, "123456789");
		String name = sp.getValue(COLUMN_NAME);
		String mobile = sp.getValue(COLUMN_MOBILE);
		TextView tv = new TextView(this);
		tv.setText("NAME:" + name + "\n" + "MOBILE:" + mobile);
		setContentView(tv);
	}	
}
```
#### 5. 其他问题
* 文件存放路径： 因为我的这个例子的pack_name 为：package com.android.SharedPreferences;
所以存放路径为：data/data/com.android.SharedPreferences/share_prefs/contacts.xml
效果图：
![img](P)  