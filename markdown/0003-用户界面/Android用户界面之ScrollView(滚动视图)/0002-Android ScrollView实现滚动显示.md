在做Android开发的PY们都知道，很多时候，由于屏幕大小的问题，很多控件我们不在一屏中显示出来，但是有些东西又是在本次页面中是必学的，那么，在这个时候，ScrollView就发挥出了他的功效了，我们可以使用ScrollView来实现滚动显示的效果！就是在一屏中不能一次性显示出来的内容，我们可以拖动滑屏来查看未显示的部分。
![img](P)  
闲话不多说，直接上Demo。此处仅上scrollview的XML配置文件，其他的部分，自己完善。效果图见附件！
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/ScrollView01"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
    <TableLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:stretchColumns="1" >
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第一节课：" />
            <EditText
                android:id="@+id/c1_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第二节课：" />
            <EditText
                android:id="@+id/c2_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第三节课：" />
            <EditText
                android:id="@+id/c3_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第四节课：" />
            <EditText
                android:id="@+id/c4_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第五节课：" />
            <EditText
                android:id="@+id/c5_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第六节课：" />
            <EditText
                android:id="@+id/c6_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第七节课：" />
            <EditText
                android:id="@+id/c7_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第八节课：" />
            <EditText
                android:id="@+id/c8_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第八节课：" />
            <EditText
                android:id="@+id/c8_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第九节课：" />
            <EditText
                android:id="@+id/c9_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <TextView
                android:gravity="right"
                android:padding="3dip"
                android:text="第十节课：" />
            <EditText
                android:id="@+id/c10_time"
                android:padding="3dip"
                android:scrollHorizontally="true" />
        </TableRow>
        <TableRow>
            <Button
                android:id="@+id/set_ok"
                android:gravity="right"
                android:padding="3dip"
                android:text="确定" />
            <Button
                android:id="@+id/set_cancle"
                android:padding="3dip"
                android:scrollHorizontally="true
                android:text="取消" />
        </TableRow>
    </TableLayout>
</ScrollView>
```
ScrollView也是一个Layout布局，可以让它内部的数据显示不下的时候出现滚动条，要注意的是不能在ScrollView中放多个组 件，如果放了多个组件，会出现如下错误：
![img](P)  