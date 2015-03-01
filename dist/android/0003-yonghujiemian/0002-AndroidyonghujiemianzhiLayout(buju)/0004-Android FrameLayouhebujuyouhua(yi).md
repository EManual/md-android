#### FrameLayout
先来看官方文档的定义：FrameLayout是最简单的一个布局对象。它被定制为你屏幕上的一个空白备用区域，之后你可以在其中填充一个单一对象 —比如，一张你要发布的图片。所有的子元素将会固定在屏幕的左上角；你不能为FrameLayout中的一个子元素指定一个位置。后一个子元素将会直接在前一个子元素之上进行覆盖填充，把它们部份或全部挡住。
我的理解是，把FrameLayout当作画布canvas，固定从屏幕的左上角开始填充图片，文字等。看看示例，原来可以利用android:layout_gravity来设置位置的：
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <ImageView
        android:id="@+id/image"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:scaleType="center"
        android:src="@drawable/candle" />
    <TextView
        android:id="@+id/text1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="@string/hello"
        android:textColor="00ff00" />
    <Button
        android:id="@+id/start"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:text="Start" />
</FrameLayout>
```
![img](P)  
#### 布局优化
使用tools里面的hierarchyviewer.bat来查看layout的层次。在启动模拟器启动所要分析的程序，再启动hierarchyviewer.bat，选择模拟器以及该程序，点击“Load View Hierarchy”，就会开始分析。可以save as png。   
#### 减少视图层级结构
从上图可以看到存在两个FrameLayout，红色框住的。如果能在layout文件中把FrameLayout声明去掉就可以进一步优化布局代码了。但是由于布局代码需要外层容器容纳，如果直接删除FrameLayout则该文件就不是合法的布局文件。这种情况下就可以使用<merge> 标签了。
修改为如下代码就可以消除多余的FrameLayout了：
```  
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android" >
    <ImageView
        android:id="@+id/image"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:scaleType="center"
        android:src="@drawable/candle" />
    <TextView
        android:id="@+id/text1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="@string/hello"
        android:textColor="00ff00" />
    <Button
        android:id="@+id/start"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:text="Start" />
</merge>
```
也有一些使用限制： 只能用于xml layout文件的根元素；在代码中使用LayoutInflater.Inflater()一个以merge为根元素的
布局文件时候，需要使用View inflate (int resource, ViewGroup root, boolean attachToRoot)指定一个ViewGroup 作为其容器，并且要设置attachToRoot 为true。
#### <include> 重用layout代码
如果在某个布局里面需要用到另一个相同的布局设计，可以通过<include> 标签来重用layout代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <include
        android:id="@+id/layout1"
        layout="@layout/relative" />
    <include
        android:id="@+id/layout2"
        layout="@layout/relative" />
    <include
        android:id="@+id/layout3"
        layout="@layout/relative" />
</LinearLayout>
```