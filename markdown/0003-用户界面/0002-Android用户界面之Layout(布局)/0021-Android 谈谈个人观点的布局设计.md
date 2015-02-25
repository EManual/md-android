框架布局是将控件组织在Android程序的用户界面中最简单的布局类型之一。
理解布局对于良好的Android程序设计来说是非常重要的。在这个教程里，你将学到所以关于框架布局的知识，它们主要用来在屏幕上组织特别的或重叠的视图控件。使用得当的话，很多有趣的Android程序用户界面都可以基于框架布局来设计。
#### 什么是框架布局
框架布局是Android开发者组织视图控件最简单和最有效的布局之一。它们使用得比其它一些布局要少一些，只是因为它们一般只用于显示单个视图，或重叠的视图。框架布局常用作容器布局，因为它一般只有一个子视图（通常是另一个布局，用于组织多个视图）。
技巧：事实上，你会看到框架布局是作为你设计的任何布局资源的父布局来使用的。如果你在层级视图工具（Hierarchy Viewer tool，一个很有用的调试你的程序布局的工具）创建你的程序，你会发现你设计的任何布局资源都被显示在一个父布局中——一个框架布局。
框架布局非常简单，这使得它们非常高效。它们可以在XML布局资源文件中定义，也可以通过Java代码在程序中定义。框架布局中的一个子视图总是被绘制到相对于屏幕的左上角上。如果存在多个子视图，那么他们被按顺序一个堆叠在另一个上面的方式绘制。这意味着第一个添加到框架布局的视图将显示在栈的底部，最后添加的视图会显示在最顶部。
让我们来看一个简单的例子。我们假设有一个框架布局大小调整到控制整个屏幕（换句话说，layout_width and layout_height属性都设置为match_parent）。我们要添加三个子控件到这个框架布局：
一个有湖面图片的ImageView。
一个在屏幕顶部显示的TextView。
一个在屏幕底部显示的（使用layout_gravity属性将TextView下沉到父布局的底部）TextView。
在XML资源文件中定义框架布局
设计程序用户界面最方便和可维护的方法是创建XML布局资源。这个方法极大地简化了UI设计过程，将很多静态创建和用户界面控件的布局以及控件属性的定义移到XML中去，取代了写代码。
XML布局资源必须存储在/res/layout项目目录下。让我们看看前一节介绍的框架布局。同样地，这个屏幕基本上就是一个有三个子视图的框架布局：一个充满整个屏幕的图片，两个文本控件绘制在它上面，每一个文本控件都是默认透明背景。这个布局资源文件命名为/res/layout/framed.xml，在XML中如下定义：
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <ImageView
        android:id="@+id/ImageView01"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:scaleType="matrix"
        android:src="@drawable/lake" >
    </ImageView>
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/top_text"
        android:textColor="000"
        android:textSize="40dp" />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:gravity="right"
        android:text="@string/bottom_text"
        android:textColor="fff"
        android:textSize="50dp" />
</FrameLayout>
```
回忆一下，在Activity中，只需要在onCreate()方法中添加一行代码来在屏幕上加载和显示布局资源。如果布局资源存放在/res/layout/framed.xml文件中，这行代码应该是：
用程序定义框架布局　　
你也可以用程序创建和配置框架布局。这通过使用FrameLayout类（android.widget.FrameLayout）来实现。你会在RelativeLayout.LayoutParams类中找到具体的参数。同样地，典型的布局参数（android.view.ViewGroup.LayoutParams），比如layout_height和layout_width，以及边距参数（ViewGroup.MarginLayoutParams），也能用在FrameLayout对象上。你必须用Java创建屏幕内容，然后向setContentView()方法提供一个包含所有要作为子视图显示的控件内容的父布局对象，而不是像前面所示直接使用setContentView()方法来加载布局资源。在这里，你的父布局就是框架布局。例如，下面的代码示例了如何用程序重新创建前面描述的相同的布局。特别地，我们在活动中实例化一个FrameLayout，并在它的onCreate()方法中先添加一个ImageView控件然后再添加两个TextView控件：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	TextView tv1 = new TextView(this);
	tv1.setText(R.string.top_text);
	tv1.setTextSize(40);
	tv1.setTextColor(Color.BLACK);
	TextView tv2 = new TextView(this);
	tv2.setLayoutParams(new LayoutParams(LayoutParams.FILL_PARENT,
			LayoutParams.WRAP_CONTENT, Gravity.BOTTOM));
	tv2.setTextSize(50);
	tv2.setGravity(Gravity.RIGHT);
	tv2.setText(R.string.bottom_text);
	tv2.setTextColor(Color.WHITE);
	ImageView iv1 = new ImageView(this);
	iv1.setImageResource(R.drawable.lake);
	iv1.setLayoutParams(new LayoutParams(LayoutParams.FILL_PARENT,
			LayoutParams.FILL_PARENT));
	iv1.setScaleType(ScaleType.MATRIX);
	FrameLayout fl = new FrameLayout(this);
	fl.setLayoutParams(new LayoutParams(LayoutParams.FILL_PARENT,
			LayoutParams.FILL_PARENT));
	fl.addView(iv1);
	fl.addView(tv1);
	fl.addView(tv2);
	setContentView(fl);
}
```