Android TextView字体类型与字体大小的设置，android textview控件，android textview字体类型，android textview位置，android textview 居中，android textview字体大小，android textview对齐。
#### 字体类型的设置
字体类型可以使用setTypeface()来设置
我们可以使用Android内部的字体，也可以通过createFromAsset()方法从外部文件来创建字体，但这种字体必须被Android系统所识别，如果无法识别，系统将会自动使用默认的字体，而且所使用的字体文件必须已经放入到了项目的assets文件夹里。
#### 字体大小的设置
字体大小可以使用 textSize="" 来设置
```  
<TextView 
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:textSize="21sp"
	android:text="How are you!"/>
```
Android TextView 背景颜色与背景图片设置，android textview 控件，android textview 背景，android textview 图片，android textview 颜色，android textview 组件，android textview background 。
设置文本颜色
```  
TextView textView = (TextView) findViewById(R.id.textview1); 
textView.setTextColor(android.graphics.Color.RED); 
// 使用实际的颜色值设置字体颜色
```
#### 设置背景颜色
有三种方法：
```  
setBackgroundResource：通过颜色资源ID设置背景色。
setBackgroundColor：通过颜色值设置背景色。
setBackgroundDrawable：通过Drawable对象设置背景色。
```
下面分别演示如何用这3个方法来设置TextView组件的背景
setBackgroundResource方法设置背景：
```  
textView.setBackgroundResource(R.color.background)；
setBackgroundColor方法设置背景：
textView.setBackgroundColor(android.graphics.Color.RED)；
setBackgroundDrawable方法设置背景：
Resources resources=getBaseContext().getResources()； 
Drawable drawable=resources.getDrawable(R.color.background)； 
textView.setBackgroundDrawable(drawable)；
```
设置背景图片
1、将背景图片放置在 drawable-mdpi 目录下，假设图片名为 bgimg.jpg 。
2、main.xml 文件
```  
<EditText 
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:background="@drawable/bgimg" />
```