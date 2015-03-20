AbsoluteLayout也就是绝对布局，我们又常称为坐标布局，在布局上灵活性比较大，也较复杂，另外由于各种手机屏幕尺寸的差异很大，给开发人员带来较多困难。
用坐标布局时，需要注意坐标原点为屏幕左上角，这和电脑屏幕的设置时一样一样的，添加视图时，要精确的计算每个视图的像素大小，最好先在纸上画草图，并把所有元素的像素定位计算好。
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="22px"
        android:layout_y="60px"
        android:src="@drawable/google" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="100px"
        android:layout_y="175px"
        android:text="I love Android" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="120px"
        android:layout_y="195px"
        android:text="I love Goolge" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="140px"
        android:layout_y="215px"
        android:text="I love the world" />
</AbsoluteLayout>
```
布局讲解：
```  
android:src="@drawable/google"
//这个是调用drawable中的图片，图片名是Google
android:layout_x="22px"
//这个是定义X轴的像素
android:layout_y="60px"
//这个是定义Y轴的像素
```
效果图：

![img](http://emanual.github.io/md-android/img/view_layout/10_layout.jpg) 
