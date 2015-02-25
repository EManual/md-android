1.定义样式
在工程的res/values目录下新加一个样式文件，文件名可以随意,后缀名必须是xml。然后每个式样有一个style节点。style结点有唯一标识名称，在style下面是item，标识了各个属性的样式值。
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="CodeFont" parent="@android:style/TextAppearance.Medium">
		<item name="android:layout_width">fill_parent</item>
		<item name="android:layout_height">wrap_content</item>
		<item name="android:textColor">00FF00</item>
		<item name="android:typeface">monospace</item>
	</style>
</resources>
```
我们要知道，每个resources的节点都会在运行时，自动转变成resource对象，然后可以通过<style>的名称来引用。例如上面的样式可以这样引用：@style/CodeFont, 样式的parent属性是可选的，它可以指向一个已经定义好的style,并且在自定义的 style里的属性值会把parent的属性值覆盖掉。大家要明白的是一个样式可以做为一个Activity的样式， 也可以做为一个Application的主题。
2.样式的继承
开发者都应该知道的一个样式可以继承平台原有的样式，也可以继承自定义的样式。继承平台原有的样式如下(一定要在parent里指定这个一定是要记住的)：
```  
<style name="GreenText" parent="@android:style/TextAppearance">
	<item name="android:textColor">00FF00</item>
</style>
```
继承自定义的样式如下(可以不采用parent属性)：这种方式只要在style的name属性上加一个前缀就可以了。前缀名是自定义的那个style的名称,例如继承CodeFont的式样可以这样写：
```  
<style name="CodeFont.Red">
	<item name="android:textColor">FF0000</item>
</style>
//这种继承方式还可以不断继承下去：
<style name="CodeFont.Red.Big">
	<item name="android:textSize">30sp</item>
</style>
```
3.使用Styles和Themes
```  
<TextView
    style="@style/CodeFont"
    android:text="@string/hello" />
//在application中使用
<application android:theme="@style/CustomTheme" >
//内置的styles 和 themes
    <activity android:theme="@android:style/Theme.Dialog" >
    <activity android:theme="@android:style/Theme.Translucent" >
    </activity>
```