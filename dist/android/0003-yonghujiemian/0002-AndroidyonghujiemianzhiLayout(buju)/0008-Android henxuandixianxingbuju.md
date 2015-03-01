理解布局对于良好的Android程序设计来说很重要。在这个教程中，你将学习到所有关于线性布局的东西，它在屏幕上垂直地或水平地组织用户界面控件或者小工具。使用得当，线性布局可以作为基本的布局，基于这个布局来可以设计出许多有趣的Android程序用户界面。
#### 什么是线性布局？
线性布局是最简单，Android开发者使用得最多的布局类型之一，开发者用它来组织你们的用户界面上的控件。线性布局的作用就像它的名字一样：它将控件组织在一个垂直或水平的形式。当布局方向设置为垂直时，它里面的所有子控件被组织在同一列中；当布局方向设置为水平时，所有子控件被组织在一行中。
线性布局可以在XML布局资源文件中定义，也可以用Java代码在程序中动态的定义。
下图展示了一个包含7个TextView控件的线性布局。这个线性布局方向被设置为垂直，导致每个TextView控件被显示在一列当中。每一个TextView控件的文本属性都是一个颜色值，背景色就是这个颜色；通过将控件的layout_width属性设置为fill_parent，每个控件都拉伸到屏幕宽度。
效果图：
![img](P)  
#### 用XML布局资源定义线性布局
设计程序用户界面最方便和可维护的方法是创建XML布局资源。这个方法极大地简化了UI设计过程，它将很多静态创建和用户界面控件的布局以及控件属性的定义移到了XML中，而不是写代码。
XML布局资源必须被存储在项目目录的/res/layout下。让我们看看前一节介绍的彩虹线性布局。这个屏幕基本上就是一个设置为铺满整个屏幕的垂直线性布局，这通过设置它的layout_width和layout_height属性为fill_parent来实现。适当地将其命名为/res/layout/rainbow.xml，XML定义如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/TextView01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".14"
        android:background="f00"
        android:gravity="center"
        android:text="RED"
        android:textColor="000" >
    </TextView>
    <TextView
        android:id="@+id/TextView02"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".15"
        android:background="ffa500"
        android:gravity="center"
        android:text="ORANGE"
        android:textColor="000" >
    </TextView>
    <TextView
        android:id="@+id/TextView03"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".14"
        android:background="ffff00"
        android:gravity="center"
        android:text="YELLOW"
        android:textColor="000" >
    </TextView>
    <TextView
        android:id="@+id/TextView04"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".15"
        android:background="0f0"
        android:gravity="center"
        android:text="GREEN"
        android:textColor="000" >
    </TextView>
    <TextView
        android:id="@+id/TextView05"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".14"
        android:background="00f"
        android:gravity="center"
        android:text="BLUE"
        android:textColor="fff" >
    </TextView>
    <TextView
        android:id="@+id/TextView06"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".14"
        android:background="4b0082"
        android:gravity="center"
        android:text="INDIGO"
        android:textColor="fff" >
    </TextView>
    <TextView
        android:id="@+id/TextView07"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight=".14"
        android:background="ee82ee"
        android:gravity="center"
        android:text="VIOLET"
        android:textColor="000" >
    </TextView>
</LinearLayout>
```
回忆一下，在Activity中，只需要一行有onCreate()方法的代码来在屏幕上加载和显示一个布局资源。如果这个布局资源被存储在/res/layout/rainbow.xml文件中，这行代码应该是：
```  
setContentView(R.layout.rainbow);
```