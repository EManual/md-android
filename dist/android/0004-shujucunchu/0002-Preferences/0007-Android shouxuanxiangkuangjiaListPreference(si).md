java代码：
```  
@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.main);
	tv = (TextView) findViewById(R.id.text1);
	setOptionText();
}
// 创建菜单，这个菜单我们在XML文件中定义 这里加载进来就OK
@Override
public boolean onCreateOptionsMenu(Menu menu) {
	MenuInflater inflater = getMenuInflater();
	// 加载我们的菜单文件
	inflater.inflate(R.menu.mainmenu, menu);
	return true;
}
// 菜单选项事件
@Override
public boolean onOptionsItemSelected(MenuItem item) {
	if (item.getItemId() == R.id.menu_prefs) {
		// 当我们点击 Settings 菜单的时候就会跳转到我们的 首选项视图，也就是我们的
		// FlightPreferenceActivity Intent intent = new
		// Intent().setClass(this,
		// xiaohang.zhimeng.FlightPreferenceActivity.class);
		// 因为我们要接收上一个Activity 就是我们的首选项视图 返回的数据，所以这里用
		// startActivityForResult()方法启动我们的首选项视图
		// 参数一：我们要跳转到哪里
		// 参数二：回传码
		this.startActivityForResult(intent, 0);
	} else if (item.getItemId() == R.id.menu_quit) {
		// 当我们点击Quit菜单退出应用程序 finish();
	}
	return true;
}
// 此方法用来 接收我们上一个Activity 也就是我们的首选项视图 返回的数据，因为我们可能会修改数据
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	super.onActivityResult(requestCode, resultCode, data);
	setOptionText();
}
// 这个方法就是用来设置我们 MainActivity 上的TextView的值(就是我们首选项的值)
private void setOptionText() {
	/*
	  * 这个方法比较有意思了 第一个参数：用来指定我们存储我们首选项值的文件的名称 格式就是
	  * 包名_preferences,大家可以看到我的包名就是xiaohang.zhimeng 这里如果你不按照这个格式写 比如你不写你当前包名
	  * 写成别的，也会生成 当前包名_preferences 这个文件 写或不写它就在那里 第二个参数：打开模式
	  */
	SharedPreferences prefs = getSharedPreferences(
			"xiaohang.zhimeng_preferences", 0);
	// 这个方法大家去看文档，否则我会越写越乱
	String option = prefs.getString(
			this.getResources().getString(
					R.string.selected_flight_sort_option),
			this.getResources().getString(
					R.string.flight_sort_option_default_value));
	// 得到我们首选项 所有选项的文本
	String[] optionText = this.getResources().getStringArray(
			R.array.flight_sort_options);
	// 设置我们 TextView要显示的值
	tv.setText("option value is " + option + "(
			+ optionText[Integer.parseInt(option)] + ")");
}
```
