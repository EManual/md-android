当你设计你的程序的时候，你可以用风格和主题来统一格式化各种屏幕和UI元素。
1、风格是一个包含一种或者多种格式化属性的集合，你可以将其用为一个单位用在布局XML单个元素当中。比如，你可以定义一种风格来定义文本的字号大小和颜色，然后将其用在View元素的一个特定的实例。
2、主题是一个包含一种或者多种格式化属性的集合，你可以将其为一个单位用在应用中所有的Activity当中或者应用中的某个Activity当中。比如，你可以定义一个主题，它为window frame和panel的前景和背景定义了一组颜色，并为菜单定义可文字的大小和颜色属性，你可以将这个主题应用在你程序当中所有的Activity里。
风格和主题都是资源。你可以用android提供的一些默认的风格和主题资源，你也可以自定义你自己的主题和风格资源。
如何新建自定义的风格和主题：
1.在res/values 目录下新建一个名叫style.xml的文件。增加一个<resources>根节点。
2.对每一个风格和主题，给<style>element增加一个全局唯一的名字，也可以选择增加一个父类属性。在后边我们可以用这个名字来应用风格，而父类属性标识了当前风格是继承于哪个风格。
3.在<style>元素内部，申明一个或者多个<item>,每一个<item>定义了一个名字属性，并且在元素内部定义了这个风格的值。
4.你可以应用在其他XML定义的资源。
#### 风格
下边是一个申明风格的实例：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style
        name="SpecialText"
        parent="@style/Text" >
        <item name="android:textSize">
			18sp
        </item>
        <item name="android:textColor">
			008
        </item>
    </style>
</resources>
```
如上所示，你可以用<item>元素来为你的风格定义一组格式化的值。在Item当中的名字的属性可以是一个字符串，一个16进制数所表示的颜色或者是其他资源的引用。
注意在<style>元素中的父类属性。这个属性让你可以能够定义一个资源，当前风格可以从这个资源当中继承到值。你可以从任何包 含这个风格的资源当中继承此风格。通常上，你的资源应该一直直接或者间接地继承Android的标准风格资源。 这样的话，你就只需要定义你想改变的值。
在这个例子当中的EditText元素，演示了如何引用一个XML布局文件当中定义的风格:
```  
<EditText
    id="@+id/text1"
    style="@style/SpecialText"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="Hello, World!" />
```
现在这个EditText组件的所表现出来的风格就为我们在上边的XML文件中所定义的那样。
#### 主题
就像风格一样，主题依然在<style>元素里边申明，也是以同样的方式引用。不同的是你通过在Android Manifest中定义的<application>和<activity>元素将主题添加到整个程序或者某个 Activity，但是主题是不能应用在某一个单独的View里。
下边是申明主题的一个例子：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="CustomTheme" >
        <item name="android:windowNoTitle">
			true
        </item>
        <item name="windowFrame">
			@drawable/screen_frame
        </item>
        <item name="windowBackground">
			@drawable/screen_background_white
        </item>
        <item name="panelForegroundColor">
			FF000000
        </item>
        <item name="panelBackgroundColor">
			FFFFFFFF
        </item>
        <item name="panelTextColor">
			?panelForegroundColor
        </item>
        <item name="panelTextSize">
			14
        </item>
        <item name="menuItemTextColor">
			?panelTextColor
        </item>
        <item name="menuItemTextSize">
			?panelTextSize
        </item>
    </style>
</resources>
```
注意我们用了@符号和？符号来应用资源。@符号表明了我们应用的资源是前边定义过的(或者在前一个项目中或者在Android 框架中)。问号？表明了我们引用的资源的值在当前的主题当中定义过。通过引用在<item>里边定义的名字可以做到(panelTextColor 用的颜色和panelForegroundColor中定义的一样)。这中技巧只能用在XML资源当中。
#### 在manifest当中设置主题
为了在成用当中所有的Activity当中使用主题，你可以打开AndroidManifest.xml 文件，编辑<application>标签，让其包含android:theme属性，值是一个主题的名字，如下：
```  
<application android:theme="@style/CustomTheme"> 
```
如果你只是想让你程序当中的某个Activity拥有这个主题，那么你可以修改<activity>标签。
Android中提供了几种内置的资源，有好几种主题你可以切换而不用自己写。比如你可以用对话框主题来让你的Activity看起来像一个对话框。在manifest中定义如下：
```  
<activity android:theme="@android:style/Theme.Dialog"> 
```
如果你喜欢一个主题，但是想做一些轻微的改变，你只需要将这个主题添加为父主题。比如我们修改Theme.Dialog主题。我们来继承Theme.Dialog来生成一个新的主题。
```  
<style name="CustomDialogTheme" parent="@android:style/Theme.Dialog"> 
```
继承了Theme.Dialog后，我们可以按照我们的要求来调整主题。我们可以修改在Theme.Dialog中定义的每个item元素的值，然后我们在Android Manifest 文件中使用CustomDialogTheme 而不是 Theme.Dialog。
#### 在程序当中设置主题
如果需要的话，你可以在Activity当中通过使用方法setTheme()来加载一个主题。注意，如果你这么做的话，你应该初始化任何View之前设置主题。比如，在调 用setContentView(View) 和inflate(int, ViewGroup)方法前。这保证系统将当前主题应用在所有的UI界面。例子如下：
```  
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setTheme(android.R.style.Theme_Light);
	setContentView(R.layout.linear_layout_3);
}
```
如果你打算在程序代码中来加载主界面的主题，那么需要注意主题当中不能包括任何系统启动这个Activity所使用的动画，这些动画将在程序启动前显示。在很多情况下，如果你想将主题应用到你的主界面，在XML中定义似乎是一个更好的办法。