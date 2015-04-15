ScrollView是一个滚动条控件，当屏幕中内容很多时候需要使用滚动条。ScrollView类的继承图如下:
```  
java.lang.Object
android.view.View
android.view.ViewGroup
android.widget.FrameLayout
android.widget.ScrollView
```
android.widget.ScrollView继承了android.widget.FrameLayout框架布局类。ScrollView例子如图所示滚动条例子。
![img](http://emanual.github.io/md-android/img/view_srollview/08_srollview.jpg)   
```
布局文件请参考代码清单：
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="@string/hello"
            android:textSize="20dip" />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="@string/content" />
    </LinearLayout>
</ScrollView>
```
ScrollView有很多属性管理它的样式，如果在xml中设置，可以在<ScrollView >标签内设置滚动条样式的属性:
```  
android:scrollbars="none"，不显示滚动条，但能够滚动的；
android:scrollbarSize，滚动条大小。
```
修改上面的例子添加这些属性xml布局文件代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:scrollbarSize="12dip"
    android:scrollbars="none" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="@string/hello"
            android:textSize="20dip" />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="@string/content" />
    </LinearLayout>
</ScrollView>
```