Style：是一个包含一种或者多种格式化属性的集合，我们可以将其用为一个单位用在布局XML单个元素当中。比如，我们可以定义一种风格来定义文本的字号大小和颜色，然后将其用在View元素的一个特定的实例。
Theme：是一个包含一种或者多种格式化属性的集合，我们可以将其为一个单位用在应用中所有的Activity当中或者应用中的某个Activity当 中。比如，我们可以定义一个Theme，它为window frame和panel的前景和背景定义了一组颜色，并为菜单定义可文字的大小和颜色属性，可以将这个Theme应用在你程序当中所有的Activity里。
定义Styles和Themes资源的XML文档的结构
对每一个Styles和Themes，给<style>元素增加一个全局唯一的名字，也可以选择增加一个父类属性。在后边我们可以用这个名字来应用风格，而父类属性标识了当前风格是继承于哪个风格。在<style>元素内部，申明一个或者多个<item>，每一个<item>定义了一个名字属性，并且在元素内部定义了这个风格的值。
```  
<?xml version="1.0" encoding="utf-8"?>   
<resources>       
<!-- 定义style -->
<style name="myTextStyle" mce_bogus="1">
	<item name="android:textSize">20px</item>
	<item name="android:textColor">EC9237</item>
</style>      
<style name="myTextStyle2" mce_bogus="1">  
	<item name="android:textSize">14px</item>
	<item name="android:textColor">FF7F7C</item>
</style>       
<!-- 定义theme -->     
<style name="myTheme" mce_bogus="1">
	<item name="android:windowNoTitle">true</item>
	<item name="android:textSize">14px</item>
	<item name="android:textColor">FFFF7F7C</item>
</style>  
</resources> 
```
从上面的例子可以看出，定义上style与定义theme基本相同，只是使用的地方不同，还有就是在一些标签的使用上： style 作用于view，theme作用于application或者Activity。
一个使用的例子：
```  
<?xml version="1.0" encoding="utf-8"?> 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:id="@+id/linear"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent" > 
<TextView  style="@style/myTextStyle"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:gravity="center_vertical|center_horizontal"
	android:text="@string/hello" />
<TextView  style="@style/myTextStyle2"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:gravity="center_vertical|center_horizontal"
	android:text="www.google.cn"
	android:autoLink="all" />
</LinearLayout> 
```
本例中既使用了style也使用了theme，这就会造成冲突，在处理冲突时我个人实验后得出的结论是 style的优先级要高于theme。
需要注意的一个标签就是android：autoLink定义它之后具有他的view会显示为蓝色带下划线的文本，并且当单击这个超链接的时候会调用系统的浏览器，启动网页，同时android的不支持html的格式。
```  
@Override public void onCreate(Bundle savedInstanceState) {  
super.onCreate(savedInstanceState);   
	setTheme(R.style.myTheme);
	setContentView(R.layout.main);
}
```
在代码中使用theme:setTheme()函数就调用了自定义的theme
在AndroidManifest.xml中应用Theme：
为了在当前所有的Activity当中使用Theme，可以打开AndroidManifest.xml文件，编辑<application>标签，让其包含android:theme属性，值是一个主题的名字，例如：
```  		
<application android:theme="@style/NewTheme">。
```
如果只是想让程序当中的某个Activity拥有这个Theme，那么可以修改<activity>标签。Android中提供了几种内置的资源，有好几种Theme你可以切换而不用自己写。比如可以用对话框Theme来让你的Activity看起来像一个对话框。在manifest中定义，例如：<activity android:theme="@android:style/Theme.Dialog">
如果喜欢一个Theme，但是想做一些轻微的改变，只需要将这个Theme添加为parent。Android SDK为我们提供了很多现成的Theme，比如：我们修改Theme.Dialog Theme，继承Theme.Dialog来生成一个新的Theme。
```  
<style parent="@android:style/Theme.Dialog"
```
继承了Theme.Dialog后，我们可以按照我们的要求来调整Theme。我们可以修改在Theme.Dialog中定义的每个item元素的值，然后我们在Android Manifest 文件中使用NewDialogTheme而不是 Theme.Dialog。