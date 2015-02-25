Android提供了许多可视的组件。通过自定义样式和主题，可以避免用这些组件开发的应用看上去千篇一律。
样式和主题都是通过预定义一系列属性值来形成统一的显示风格。区别是，样式只能应用于某种类型的View；而主题刚好相反，它不能应用于特定的View，而只能作用于一个或多个Activity，或是整个应用。
以下结合具体例子说明如何定义样式和主题：
1.定义样式和主题
在工程中res/values/下添加styles.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
<!-- 定义my_style_1，没有指定parent，用系统缺省的 -->
<style name="my_style_1">
  <!-- 定义与指定View相关的若干属性 -->
  <item name="android:hint">load from style 1</item>
</style>
<!-- 定义my_style_2，用自定义的my_style_1作为parent -->
<style name="my_style_2" parent="@style/my_style_1">
  <!-- 定义与指定View相关的若干属性 -->
  <item name="android:textSize">30sp</item>
  <item name="android:textColor">FFFF0000</item>
  <item name="android:hint">load from style 2</item>
</style>
<!-- 定义my_style_3，用android的EditText作为parent -->
<style name="my_style_3" parent="@android:style/Widget.EditText">
  <!-- 定义与指定View相关的若干属性 -->
  <item name="android:hint">"load from style 3"</item>
  <item name="android:textStyle">bold|italic</item>
  <item name="android:typeface">monospace</item>>
  <item name="android:background">@drawable/mybackground</item>
</style>
<!-- 定义MyTheme，用android的Theme作为parent -->
<style name="MyTheme" parent="@android:style/Theme">
  <item name="android:textSize">20sp</item>
  <item name="android:textColor">FF0000FF</item>
  <item name="android:hint">"load from style 3"</item>
  <item name="android:textStyle">bold|italic</item>
  <item name="android:typeface">monospace</item>>
  <item name="android:background">@drawable/gallery_selected_pressed</item>
  <item name="myStyle">@style/my_style_3</item>
</style>
</resources>
```
主题和样式的定义方法类似，都是在<style>下添加N个<item>。
<style>下有两个有用的属性：
name - 以后引用时会用到；
parent - 可选，一些在自定义的style中没有指定的属性会继承parent style中的值。parent可以是android预定义的resource，也可以是自己定义的style。
<item>下定义需要改变的属性值。Android中能使用的属性可以在<sdk>/docs/reference/android/R.styleable.html中查到；也可以用自己定义的属性值；
2.使用主题
a)从AndroidManifest中指定，可以选择Application应用级：
```  
<application android:theme="@style/MyTheme">
```
或是Activity级：
```  
<activity android:theme="@style/MyTheme">
```
b)从Java代码中指定：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
    setTheme(R.style.MyTheme);
	setContentView(R.layout.main);
}
```
注意：setTheme必须在setContentView(),addContentView()或inflate()等实例化View的函数之前调用！
3.使用样式
a)从layout的xml文件中指定：
```  
<EditText android:id="@+id/EditText03" 
	style="@style/my_style_3"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content">
</EditText>
```
b)从Java代码中指定：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setTheme(R.style.MyTheme);
	setContentView(R.layout.main);
	LinearLayout ll = (LinearLayout)findViewById(R.id.llMain);
	EditText et = new EditText(this, null, R.attr.myStyle);
	ll.addView(et);
}
```