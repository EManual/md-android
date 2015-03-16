表格布局：是一个ViewGroup以表格显示它的子视图（view）元素，即行和列标识一个视图的位置。其实Android的表格布局跟HTML中的表格布局非常类似，TableRow 就像HTML表格的[tr]标记。
用表格布局需要知道以下几点：
1、android:shrinkColumns，对应的方法：setShrinkAllColumns(boolean)，作用：设置表格的列是否收缩（列编号从0开始，下同），多列用逗号隔开（下同），如android:shrinkColumns=”0,1,2″，即表格的第1、2、3列的内容是收缩的以适合屏幕，不会挤出屏幕。
2、android:collapseColumns，对应的方法：setColumnCollapsed(int,boolean)，作用：设置表格的列是否隐藏
3、android:stretchColumns，对应的方法：setStretchAllColumns(boolean)，作用：设置表格的列是否拉伸
看下面的res/layour/main.xml：
```  
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:shrinkColumns="0,1,2" >
 	<!-- have an eye on ! -->
    <TableRow>
 		<!-- row1 -->
        <Button
            android:id="@+id/button1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="0"
            android:text="Hello, I am a Button1" />
        <Button
            android:id="@+id/button2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Hello, I am a Button2" />
    </TableRow>
    <TableRow>
 		<!-- row2 -->
        <Button
            android:id="@+id/button3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Hello, I am a Button3" />
        <Button
            android:id="@+id/button4"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Hello, I am a Button4" />
    </TableRow>
    <TableRow>
        <Button
            android:id="@+id/button5"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="2"
            android:text="Hello, I am a Button5" />
    </TableRow>
</TableLayout>
```

![img](http://emanual.github.io/md-android/img/view_layout/13_layout.jpg) 