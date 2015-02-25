Android上的Style分为了两个方面：
1. Theme是针对窗体级别的，改变窗体样式；
2. Style是针对窗体元素级别的，改变指定控件或者Layout的样式。
Android系统的themes.xml和style.xml(位于\base\core\res\res\values\)包含了很多系统定义好的style，建议在里面挑个合适的，然后再继承修改。
以下属性是在Themes中比较常见的，源自Android系统本身的themes.xml：
```  
<!-- Window attributes -->
<item name="windowBackground">@android:drawable/screen_background_dark</item>
<item name="windowFrame">@null</item>
<item name="windowNoTitle">false</item>
<item name="windowFullscreen">false</item>
<item name="windowIsFloating">false</item>
<item name="windowContentOverlay">@android:drawable/title_bar_shadow</item>
<item name="windowTitleStyle">@android:style/WindowTitle</item>
<item name="windowTitleSize">25dip</item>
<item name="windowTitleBackgroundStyle">@android:style/WindowTitleBackground</item>
<item name="android:windowAnimationStyle">@android:style/Animation.Activity</item>
```
至于Android的style就比较的广泛了，如果有过web css开发经典的人来说，应该看一下就会懂的，当然也有它不同之处了。
Android的style文件我们是放在res/values目录下面的，当然它是一个 xml文件，根节点是：resources. 下面是一个示例：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="TextStyle">
		<item name="android:textSize">14sp</item>
		<item name="android:textColor">fff</item>
	</style>
</resources>
```
在style里面只需要定义需要改变的属性，不作设定的程序会自动引用系统默认的属性。
在布局文件中我们怎么引用呢？
```  
<EditText id="@+id/editText1"
	style="@style/TextStyle"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:text="Hello, World!" />
```
其它经过引用style后的EditText定义为：
```  
<EditText id="@+id/editText1"
	android:layout_textSize="14sp"
	android:textColor="fff"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:text="Hello, World!" />
```
这样就更好的理解了吧？到于style item中的name应该怎么写？我想你也能知道了吧？
以上只是style的一些简单的应用，下在将会讲到一个非常实用的知识，也就是style的继承关系。这样才能更好的简化我们代码的工作量，也更利用整个程序逻辑的组建。它的继承关系可以有两种实现的方式：
1. 是通过 parent属性来指定
2. 通过点号来指定
接下来我们分别来举例：
我们程序中应用到最多的可能就是TextView了，它可能会有很多种情况，比如作为title,正文，提示等等，而这一些的TextView有他的共同点，也有他们的不同之处。
首先我们定义一个通过的style:
```  
<style name="TextStyle">
	<item name="android:shadowDx">-0.5</item>
	<item name="android:shadowDy">1</item>
	<item name="android:shadowRadius">0.5</item>
	<item name="android:singleLine">true</item>
	<item name="android:ellipsize">marquee</item>
</style>
```
以上主要是定义了他的阴影啊，单行啊，超过长度怎么办啊。
接下来我们再定义一个title级别的样式，title我们也想要这些属性，那么就得继承它了。
首先我们用 parent属性来继承
```  
<style name="TextTitle" parent="TextStyle">
	<item name="android:textSize">18sp</item>
	<item name="android:textColor">fff</item>
	<item name="android:textStyle">bold</item>
</style>
```
parent属性中跟的就是父类的名称，就样title的阴影 ，字体大小 ，辨色，粗细就一起出来了，而我们不用再去定义title的阴影了。节省了不少的时间。
第二种继承是利用parentStyle.childStyle的方式 ，用点号来继承 ，上面的TextTitle我们也可以这样写：
```  
<style name="TextStyle.TextTitle">
	 <item name="android:textSize">18sp</item>
	 <item name="android:textColor">fff</item>
	 <item name="android:textStyle">bold</item>
</style>
```
这样也能得到预期的效果。这样做不爽的地方就是名字就长了，我们在引用这个style的时候，就得style="@style/TextStyle.TextTitle"，如果继承的层级越多，这个名字就会越长。
怎么样？看懂了吧？这只是一些简单的应用，当然想制作出更漂亮的style就得自己动手去多练习一下，经验是不断的尝试积累出来的。