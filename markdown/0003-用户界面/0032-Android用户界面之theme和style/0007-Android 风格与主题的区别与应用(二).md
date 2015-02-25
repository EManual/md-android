应用的编写：
```  
<TextView
    style="@style/SpecialText2"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="@string/hello" />
<EditText
    android:id="@+id/EditText01"
    style="@style/SpecialText"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@+id/EditText01" >
</EditText>
```
主题　　
就像Style一样，Theme依然在<style>元素里边申明，也是以同样的方式引用。不同的是通过在Android Manifest中定义的<application>和<activity>元素将主题添加到整个程序或者某个 Activity，但是主题是不能应用在某一个单独的View里。
下边是SDK中主题的一个例子：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="CustomTheme">
		<item name="android:windowNoTitle">true</item>
		<item name="windowFrame">@drawable/screen_frame</item>
		<item name="windowBackground">@drawable/screen_background_white</item>
		<item name="panelForegroundColor">FF000000</item>
		<item name="panelBackgroundColor">FFFFFFFF</item>
		<item name="panelTextColor">?panelForegroundColor</item>
		<item name="panelTextSize">14</item>
		<item name="menuItemTextColor">?panelTextColor</item>
		<item name="menuItemTextSize">?panelTextSize</item>
	</style>
</resources>
```
注意我们用了@符号和?符号来应用资源。@符号表明了我们应用的资源是前边定义过的(或者在前一个项目中或者在Android 框架中)。问号?表明了我们引用的资源的值在当前的主题当中定义过。通过引用在<item>里边定义的名字可以做到 (panelTextColor 用的颜色和panelForegroundColor中定义的一样)。这中技巧只能用在XML资源当中
在程序中使用主题的方法：
```  
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setTheme(android.R.style.Theme_Light);
	setContentView(R.layout.linear_layout_3);
}
```