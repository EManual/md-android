代码如下：
```  
public void onTabReselected(Tab tab, FragmentTransaction ft) {
}
```
接下来是文中涉及的3个xml布局文件:　　 
相关的XML布局action_bar_display_options.xml代码为
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/toggle_home_as_up"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toggle_home_as_up" />
    <Button
        android:id="@+id/toggle_show_home"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toggle_show_home" />
    <Button
        android:id="@+id/toggle_use_logo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toggle_use_logo" />
    <Button
        android:id="@+id/toggle_show_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toggle_show_title" />
    <Button
        android:id="@+id/toggle_show_custom"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toggle_show_custom" />
    <Button
        android:id="@+id/toggle_navigation"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/toggle_navigation" />
    <Button
        android:id="@+id/cycle_custom_gravity"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/cycle_custom_gravity" />
</LinearLayout>
```
相关的自定义布局文件action_bar_display_options_custom.xml代码为
```  
<?xml version="1.0" encoding="utf-8"?>
<Button xmlns:android="http://schemas.android.com/apk/res/android"
	android:text="@string/display_options_custom_button" />
```
相关的menu布局xml文件display_options_actions.xml代码为
```  
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
	<item android:id="@+id/simple_item"
		android:title="@string/display_options_menu_item" />
</menu>
```
上面的string后的元素大家可以根据自己的情况进行替换。　　
ActionBar相关的示例代码第二部分分为两种，作为Android 3.0的重要特性我们直接看代码:
#### 一、使用菜单资源构造
```  
public class ActionBarMechanics extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		getWindow().requestFeature(Window.FEATURE_ACTION_BAR);
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		menu.add("Normal item");
		MenuItem actionItem = menu.add("Action Button");
		actionItem.setShowAsAction(MenuItem.SHOW_AS_ACTION_IF_ROOM);
		actionItem.setIcon(android.R.drawable.ic_menu_share);
		return true;
	}
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		Toast.makeText(this, "Selected Item: " + item.getTitle(),
				Toast.LENGTH_SHORT).show();
		return true;
	}
}
```