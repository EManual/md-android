如果视图界面风格需要统一的规划，就需要使用android视图技术中的style。这类似HTML技术和CSS技术的关系。
示例改编自简单使用SimpleCursorAdapter。示例截图如下：
![img](P)  
这里将标题字体放大，并且加粗。如果不用style可以这样写：
```  
<TextView
    android:id="@+id/riverName"
    android:layout_width="match_parent
    android:layout_height="wrap_content""
    android:textSize="25sp"
    android:textStyle="bold" />
```
这样的缺点是在众多布局文件中要写很多重复的代码，而且修改的时候也会造成麻烦。
style的做法，是将这些style内容写到单独的xml文件中，放置在res/values目录下：
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<style name="itemTitle">
		<item name="android:textSize">25sp</item>
		<item name="android:textStyle">bold</item>
	</style>
</resources>
```
在布局文件中的引用：
```  
<TextView
    android:id="@+id/riverName"
    style="@style/itemTitle"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
在在视图显示中使用Style中，使用的是针对一个视图指定元素的样式。如果要针对整个Activity，对它的背景颜色和字体等做统一的样式约定，就需要使用另外一个技术，theme。
以下示例就是在在视图显示中使用Style基础上增加了theme。
![img](P)  
首先，要编写theme文件，和style文件类似，是放在res/values目录下：
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<color name="custom_background_color">FFFFFFFF</color>
	<style name="RiverTheme" parent="android:Theme.Light">
		<item name="android:windowBackground">@color/custom_background_color</item>
	</style>
</resources>
```
这里继承了系统的theme.light，一般theme是继承的，这样可以对默认的风格不必重复定义。
本例定义了一个背景色。这里背景色要单独声明，不能在item元素中直接写颜色值，会提示语法错误。
引用该theme，在manifest文件中指定的Activity中：
```  
<activity
    android:name=".ListViewActivity"
    android:label="@string/app_name"
    android:theme="@style/RiverTheme" >
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```