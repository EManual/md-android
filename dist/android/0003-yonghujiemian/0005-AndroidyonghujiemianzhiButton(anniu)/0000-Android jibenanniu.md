本文向你展示了在你的Android应用程序中创建一个简单的Button或ImageButton控件的步骤。首先，你会学到如何向你的布局文件中添加按钮控件。然后你会学习如何用两种方法处理用户对按钮的点击。最后，我们讨论Android中按钮控件一些其它的可用特性。
#### 第1步：创建Android应用程序
我们从创建Android程序开始。你平常一样完成你的Android应用。一旦你已经创建项目并可以运行，决定你希望向什么样的屏幕添加Button控件。可能你就简单地创建了一个使用默认活动和布局(main.xml)的新Android项目。这个教程将使用这种情况作例子。一旦你创建了你的Android项目，你就可以继续学习这篇文章了。
#### 第2步：使用Button控件
Android SDK包含两个在你的布局中可以使用的简单按钮控件：Button（android.widget.Button）和ImageButton（android.widget.ImageButton）。这些控件的功能很相似因此我们几乎可以一并地的讨论它们。这两个控件不相同的地方基本上就是外观上；Button控件有一个文本标签，而ImageButton使用一个可绘制的图像资源来代替。Button使用的一个很好的例子应该是一个简单的带有“保存”文本标签的按钮。ImageButton使用的一个很好的例子可能是音乐播放器按钮的集合，包括播放P, 暂停 以及停止。
这里是一个示例屏幕，包括一个Button控件（左边）和一个ImageButton控件（右边）。
效果图：
![img](P)  
Android SDK还包含了一些其它更不为人知的从上面两个基本按钮类型继承来的类按钮控件，包括CompoundButton，RadioButton，ToggleButton，和ZoomButton。要了解这些控件的更多信息，查看Android文档。你也可以通过继承合适的类并实现控件行为来创建自定义控件。
#### 第3步：向布局添加Button控件
Button控件通常都被作为活动的布局资源文件一部分。比如，要添加一个Button控件到与你程序相关的main.xml布局资源中，你必须编辑布局文件。你可使用Eclipse的布局资源设计器，或者直接编辑XML。像按钮这样的控件也可以通过程序动态地创建并在运行时添加到你的屏幕上。简单地通过它的类来创建合适的控件并将它添加到你的活动中的布局。
要添加一个Button控件到布局资源文件，打开/res/layout/main.xml布局文件，它是你的Android项目的一部分。点击你想要为其添加Button控件的LinearLayout （或者父级布局控件，比如RelativeLayout或FrameLayout）。在Eclipse中，你可以点击Outline标签中的父级布局，然后使用绿色加号按钮添加一个新的控件。选择你要添加的控件——在这个例子中是Button控件。
效果图：
![img](P)  
要配置Button控件的外观，选中该控件并通过在属性标签中改变属性值来调节控件的属性。下面是一些你会想知道的特别的属性：
使用id属性给Button或ImageButton一个唯一的名字。
使用文本属性设置Button控件上要显示的文字；使用src属性设置ImageButton控件上要显示的图片。
将控件的布局高度和布局宽度属性设置为wrap_content.
设置任何其它属性来调整控件的外观。比如，使用文本颜色，文本大小和文本样式属性来调整Button的字体。
下面是用来生成前段中展示的屏幕的布局资源文件的内容。它包括三个控件。RelativeLayout组织屏幕上的控件，也就是两个子控件，一个Button和一个ImageButton，如下
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center" >
    <Button
        android:id="@+id/Button01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:minHeight="92dp"
        android:onClick="onMyButtonClick"
        android:text="@string/hello"
        android:textSize="22dp" >
    </Button>
    <ImageButton
        android:id="@+id/ImageButton01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@+id/Button01"
        android:src="@drawable/skater" >
    </ImageButton>
</RelativeLayout>
```
#### 第4步：处理点击
现在，如果你运行你的程序，按钮控件显示出来了，但是如果你点击它们不会有任何反应。现在应该来处理控件上的点击事件了。有好几种方法可以做到。
让我们从简单的方法开始吧。Button和ImageButton控件有一个叫onClick的属性（在属性面板里叫“On Click”）。你可以通过这个属性设置要处理点击事件的方法名，然后在你的活动中实现这个方法。比如，你可以将你的Button控件属性设置为onMyButtonClick。在XML中，这个属性将如下所示：
```  
android:onClick="onMyButtonClick
```
然后，在你的活动类，你需要实现这个方法。它应该是一个带有单个参数（一个View对象）的公有的void方法。例如，下面的按钮点击实现了当Button控件被点击时在屏幕生成一个消息框：
```  
public void onMyButtonClick(View view){
	Toast.makeText(this, "Button clicked!", Toast.LENGTH_SHORT).show();
}
```
当你点击这个Button控件，onMyButtonClick()方法被调用，在屏幕上显示一个消息。你的Button按钮能做什么就取决于你自己了。下图显示了当点击Button按钮时消息是如何展示的：
效果图：
![img](P)  
#### 第5步：处理点击——实现OnClickListener
实现点击事件处理的另一种方法是使用setOnClickListener()方法向你的按钮控件注册一个新的View.OnClickListener。这种方式代替了将你布局资源中的按钮控件的On Click属性设置为一个你必须实现的方法的方式，你可以在你的活动中动态地做这些事情。虽然这可能看起来有很多额外的代码要写，但至少理解它是非常重要的，因为在一些控件上点击不是需要处理的唯一事件。我们将要向你展示的程序应用了其它的事件，比如长按。
要使用这个方法，你必须更新你的活动类以注册控件点击事件。通常情况下通过你的活动的onCreate()方法来实现。使用findViewById()方法找到控件然后使用它的setOnClickListener()方法来定义当它被点击时的行为。你将需要自己去实现界面的onClick()方法。比如，下面的代码（位于活动的onCreate()方法中）为我们的ImageButton控件注册了一个点击处理器。
```  
ImageButton myImageButton = (ImageButton) findViewById(R.id.ImageButton01);
	myImageButton.setOnClickListener(new View.OnClickListener() {
	public void onClick(View v) {
	Toast.makeText(BasicButtonActivity.this, "ImageButton clicked!", Toast.LENGTH_SHORT).show();
	}
});
```
同样地，你可以使用这个技术来实现长按点击处理，通过使用控件的setOnLongClickListener()方法。
#### 总结
按钮控件在Android程序中经常会用到。在这个快速教程中你学习了如何创建两种不同的Android按钮控件：Button和ImageButton。你也学习了实现这些类型按钮控件的按钮点击事件处理的几种方法。