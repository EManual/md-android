#### 准备工作    
要想开发Android程序，我们需要如下三种软件：
```  
1. Eclipse
2. Android SDK
3. 开发Android程序的Eclipse 插件
```
Activity类每一种移动开发环境都有自己的基类。如j2me应用程序的基类是midlets，BREW的基类是applets，而Android程序的基类是 Activity。这个activity为我们提供了对移动操作系统的基本功能和事件的访问。这个类包含了基本的构造方法，键盘处理，挂起来恢复功能，以 及其他底层的手持设备的访问。实质上，我们的应用程序将是一个Activity类的扩展。在本文中读者将会通过例子学习到如何使用Activity类来编 写Android程序。下面是一个简单的继承Activity的例子。
```  
public class LocateMe extends Activity {
	public void onCreate(Bundle params) {
		super.onCreate(params);
		setContentView(R.layout.main);
	}
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		return true;
	}
}
```
目录三、View类    View类是Android的一个超类，这个类几乎包含了所有的屏幕类型。但它们之间有一些不同。每一个view都有一个用于绘画的画布。这个画布可以用来进行任意扩展。本文为了方便起见，只涉及到了两个主要的View类型：定义View和Android的XML内容View。在上面的代码中，使用的是 “Hello World” XML View，它是以非常自然的方式开始的。
如果我们查看一下新的Android工程，就会发现一个叫main.xml的文件。在这个文件中，通过一个简单的XML文件，描述了一个屏幕的布局。这个简单的xml文件的内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_centerHoriz=""
        android:text="ress the center key to locate yourself" />
</RelativeLayout>
```
上面的内容的功能看起来非常明显。这个特殊文件定义了一个相关的布局，这就意味着通过一个元素到另一个元素的关系或是它们父元素的关系来描述。对于视图来说，有一些用于布局的方法，但是在本文中只关注于上述的xml文件。
RealtiveLayout中包含了一个填充整个屏幕的文本框（也就是我们的LocateMe activity）。这个LocateMe activity在默认情况下是全屏的，因此，文本框将继承这个属性，并且文本框将在屏幕的左上角显示。另外，必须为这个XML文件设置一个引用数，以便Android可以在源代码中找到它。在默认情况下，这些引用数被保存在R.Java中。
```  
public final class R {
	public static final class layout {
		public static final int main = 0x7f030001;
	}
}
```
视图也可以被嵌套，但和J2ME不同，我们可以将定制的视图和Android发布的Widgets一起使用。开发人员选择GameCanvas。这就意味着如果我们想要一个定制的效果，就必须在GameCanvas上重新设计我们所有的widget。 
Android还不仅仅是这些，视图类型也可以混合使用。Android还带了一个widget库，这个类库包括了滚动条，文本实体，进度条以及其他很多控件。这些标准的widget可以被重载或被按着我们的习惯定制。现在让我们来进入我们的例子。  
```  
public class LocateMe extends Activity {
	public void onCreate(Bundle params) {
		super.onCreate(params);
		setContentView(R.layout.main);
	}
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		return true;
	}
}
```
View类、View类是Android的一个超类，这个类几乎包含了所有的屏幕类型。但它们之间有一些不同。每一个view都有一个用于绘画的画布。这个画布可以用来进行任意扩展。本文为了方便起见，只涉及到了两个主要的View类型：定义View和Android的XML内容View。在上面的代码中，使用的是 “Hello World” XML View，它是以非常自然的方式开始的。
如果我们查看一下新的Android工程，就会发现一个叫main.xml的文件。在这个文件中，通过一个简单的XML文件，描述了一个屏幕的布局。这个简单的xml文件的内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_centerHoriz=""
        android:text="ress the center key to locate yourself" />
</RelativeLayout>
```