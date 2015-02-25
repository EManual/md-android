我们大家都知道SlidingDrawer 这个类，也就是所谓的"抽屉"类。它的用法很简单，要包括handle和content。handle 就是当你点击它的时候，content 要么抽抽屉要么关抽屉。别的不多说了，我们就来看看代码吧：
设置main.xml布局:代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="808080"
    android:orientation="vertical" >
    <SlidingDrawer
        android:id="@+id/slidingdrawer"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:content="@+id/content"
        android:handle="@+id/handle"
        android:orientation="vertical" >
        <Button
            android:id="@+id/handle"
            android:layout_width="88dip"
            android:layout_height="44dip"
            android:background="@drawable/handle" />
        <LinearLayout
            android:id="@+id/content"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:background="00ff00" >
            <Button
                android:id="@+id/button"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Button" />
            <EditText
                android:id="@+id/editText"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" />
        </LinearLayout>
    </SlidingDrawer>
</LinearLayout>
```
设置handle 图标的样式，在drawable 里添加handle.xml ,代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/handle_normal" android:state_enabled="true" android:state_window_focused="false"/>
    <item android:drawable="@drawable/handle_pressed" android:state_pressed="true"/>
    <item android:drawable="@drawable/handle_focused" android:state_enabled="true" android:state_focused="true"/>
    <item android:drawable="@drawable/handle_normal" android:state_enabled="true"/>
    <item android:drawable="@drawable/handle_focused" android:state_focused="true"/>
</selector>
```
效果图：
![img](P)  
![img](P)  
这就是前后的对照。