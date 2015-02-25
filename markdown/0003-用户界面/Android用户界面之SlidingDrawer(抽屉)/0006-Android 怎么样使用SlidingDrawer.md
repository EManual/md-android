我们先来看看效果图：
![img](P)  
![img](P)  
SlideDrawer是多个（两个页面）的一种显示方式。如上左图所示，普通的，我们显示Hello的Label，当我们按下面的SlidingDrawer的ImageView，即右图所示图标时，可以将SlideDrawer的内容显示上去，如中图。SlidingDrawer可以在Open和Close两个状态之间切换。Open时覆盖，不是所有的Layout都能支持这种叠加覆盖，作为SlidingDrawer的container，例如LinearLayout就不可以，一般使用RelativeLayout和FrameLayout。下面是例子的XML文件，采用了FrameLayout作为容器：
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="FF8888CC " >
    <!-- 我们采用了简单的TextView表示原本页的内容，在SlidingDrawer处于Close时显示，如左图 -->
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:gravity="center"
        android:text="Hello"
        android:textStyle="bold" />
    <!-- SlidingDrawer中包含两个重要参数handle和content，类似于Tab的标签和内容，我们需要在这里设定相关的id，以便引用 -->
    <SlidingDrawer
        android:id="@+id/c96_drawer"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:content="@+id/c96_content"
        android:handle="@+id/c96_handle" >
        <!-- 和SlidingDrawer的handle的id相一致，一般而言会是一个Image，当然我们也可以设置为Button等其他，只是美观上欠佳。 -->
        <ImageView
            android:id="@id/c96_handle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/tray_handle_normal" />
        <!-- 和SlidingDrawer的content的id相一致，这里我们简单用Button来处理 -->
        <Button
            android:id="@id/c96_content"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:text="I am in Here" />
    </SlidingDrawer>
</FrameLayout>
```
源文件无须特别处理，对于SlidingDrawer，可以open(),close(),toggle()，也可以采用动画的方式anmiateOpen(),animateClose()和animateToggle()。可以在XML中设置android:animateOnClick ="true" ，缺省为真。
我们还可以设置lock()和unlock()。
我们可以对SlidingDrawer的事件进行监听：openen,closed,scrolled（用户拖拽标签）。例如：setOnDrawerCloseListener(SlidingDrawer.OnDrawerCloseListener onDrawerCloseListener)。 