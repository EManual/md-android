#### 三、重要方法
```  
animateClose()：关闭时实现动画。
close()：即时关闭
getContent()：获取内容
isMoving()：指示SlidingDrawer是否在移动。
isOpened()：指示SlidingDrawer是否已全部打开
lock()：屏蔽触摸事件。
setOnDrawerCloseListener(SlidingDrawer.OnDrawerCloseListener onDrawerCloseListener)：SlidingDrawer关闭时调用
unlock()：解除屏蔽触摸事件。
toggle()：切换打开和关闭的抽屉SlidingDrawer。
```
上面例子的布局文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="效果显示：" />
        <SlidingDrawer
            android:id="@+id/drawer1"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:content="@+id/mycontent1"
            android:handle="@+id/layout1"
            android:orientation="horizontal" >
            <LinearLayout
                android:id="@id/layout1"
                android:layout_width="35sp"
                android:layout_height="wrap_content"
                android:gravity="center"
                android:orientation="vertical" >
                <ImageView
                    android:id="@+id/myImage"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:src="@drawable/open" />
            </LinearLayout>
            <GridView
                android:id="@id/mycontent1"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:gravity="center"
                android:numColumns="2" />
        </SlidingDrawer>
    </LinearLayout>
</FrameLayout>
```