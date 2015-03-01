在Android 3.0中除了我们重点讲解的Fragment外，Action Bar也是一个重要的内容，Action Bar主要是用于代替传统的标题栏，对于Android平板设备来说屏幕更大它的标题使用Action Bar来设计可以展示更多丰富的内容，方便操控。
Action Bar主要功能包含:
1. 显示选项菜单。
2. 提供标签页的切换方式的导航功能，可以切换多个fragment。
3. 提供下拉的导航条目。
4. 提供交互式活动视图代替选项条目。
5. 使用程序的图标作为返回Home主屏或向上的导航操作。
提示在你的程序中应用ActionBar需要注意几点，SDK和最终运行的固件必须是Android3.0即honeycomb，在androidmanifest.xml文件中的uses-sdk元素中加入android:minSdkVersion 或android:targetSdkVersion，类似
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="net.androidchina.example"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="honeycomb" />
    <application>
    </application>
</manifest>
```
如果需要隐藏Action Bar可以在你的Activity的属性中设置主题风格为NoTitleBar在你的manifest文件中，下面的代码在3.0以前是隐藏标题，而在3.0以后就是隐藏ActionBar了，代码为
```  
<activity android:theme="@android:style/Theme.NoTitleBar">
```
#### 一、添加活动条目 Action Items
对于活动条目大家可以在下图看到Android 3.0的标题右部分可以变成工具栏，下面的Save和Delete就是两个Action Items活动条目。
下面是一个menu的layout布局文件代码
```  
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:id="@+id/menu_add"
        android:icon="@drawable/ic_menu_save"
        android:showAsAction="ifRoom|withText"
        android:title="@string/menu_save"/>
</menu>
```
而其他代码类似Activity中的Menu，比如
```  
@Override
public boolean onOptionsItemSelected(MenuItem item) {
	switch (item.getItemId()) {
	case android.R.id.home:
		// 当Action Bar的图标被单击时执行下面的Intent
		Intent intent = new Intent(this, Android123.class);
		startActivity(intent);
		break;
	}
	return super.onOptionsItemSelected(item);
}
```
对于ActionBar的创建，可以在你的Activity中重写onStart方法
```  
@Override
protected void onStart() {
	super.onStart();
	ActionBar actionBar = this.getActionBar();
	actionBar.setDisplayOptions(ActionBar.DISPLAY_HOME_AS_UP,
			ActionBar.DISPLAY_HOME_AS_UP);
}
```
调用getActionBar方式在你的Activity的onCreate中时需要注意必须在调用了setContentView之后。
#### 二、添加活动视图 Action View
对于ActionView，我们可以在menu的布局文件使用中来自定义searchview布局，如代码
```  
<item android:id="@+id/menu_search"
	android:title="Search"
	android:icon="@drawable/ic_menu_search"
	android:showAsAction="ifRoom"
	android:actionLayout="@layout/searchview" />
```
也可以直接指定Android系统中的SearchView控件，那么这时menu"_search的代码要这样写
```  
<item android:id="@+id/menu_search"
	android:title="Search"
	android:icon="@drawable/ic_menu_search"
	android:showAsAction="ifRoom"
	android:actionViewClass="android.widget.SearchView" />
```
大家注意上面的两种方法中一个属性是actionLayout制定一个layout xml布局文件，一个是actionViewClass指定一个类，最终调用可以在Activity中响应onCreateOptionsMenu方法映射这个menu布局即可。
```  
@Override
public boolean onCreateOptionsMenu(Menu menu) {
	getMenuInflater().inflate(R.menu.options, menu);
	SearchView searchView = (SearchView) menu.findItem(R.id.menu_search)
			.getActionView();
	return super.onCreateOptionsMenu(menu);
}
```