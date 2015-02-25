style的使用
当Activity中很多组件的一些属性相同时，可以运用style来进行统一的设置，比如Button，EditText，TextView上的字体的大小和颜色相同，就可以用style进行统一的设置
示例：
在value中添加style.xml文件
```  
<?xml version="1.0" encoding="UTF-8"?>
<resources>
	<style name="TextStyle">
		<item name="android:textSize">8dip</item>
		<item name="android:textColor">ffffff</item>
	</style>
</resources>
```
Activity里面有四个组件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
<TextView  
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    style="@style/TextStyle"
    android:text="@string/hello"/>
    <Button
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		style="@style/TextStyle"
		android:text="@string/hello"/>
    <Button
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		style="@style/TextStyle"
		android:text="@string/hello" />
    <TextView
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		style="@style/TextStyle"
		android:text="@string/hello"/>
</LinearLayout>
```
2.2theme的使用
步骤一：设置应用到整个工程中或者某个activity中的主题
主题依然在<style>标签中声明，但是不同的是，主题是通过在manifest文件中应用到<appliaction>和<activity>中从而应用到整个工程或者某一个Activity中的
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="NoTitle">
		<item name="android:windowNoTitle">true</item>
	</style>
</resources>
```
步骤二：在manifest文件中设置主题
在你所装的SDK的这个目录android-sdk-windows\platforms\android-8\data\res\values有个themes.xml的文件里可以看到你所使用的SDK版本的theme属性值
在标签中添加属性android:theme,<application android:theme="@style/CustomTheme">
这样就将Activity中的标题栏去掉了
系统自带的主题应该这样注册：
```  
android:theme="@android:style/Theme.Dialog"
```
一般前面加上android都表示系统自带的
@表明应用的资源是在资源中已经定义过的，
？表示引用的资源是在当前的主题中定义过的
如果你喜欢一个主题，只想做些简单的改变，则可以将这个主题设置为父主题，比如
```  
<style name="CustomDialogTheme" parent="@android:style/Theme.Dialog">
```
继承了Theme.Dialog后，我们可以修改Theme.Dialog中的每一个item值
几个系统自带的theme
1.将窗口最大化
```  
<style name="Theme.NoTitleBar">
    <item name="android:windowNoTitle">true</item>
</style>
```
2.用来将一个Activity设置成dialog形式
```  
<style name="Theme.Dialog">
	<item name="android:windowFrame">@null</item>
	<item name="android:windowTitleStyle">@android:style/DialogWindowTitle</item>
	<item name="android:windowBackground">@android:drawable/panel_background</item>
	<item name="android:windowIsFloating">true</item>
	<item name="android:windowContentOverlay">@null</item>
</style>
```