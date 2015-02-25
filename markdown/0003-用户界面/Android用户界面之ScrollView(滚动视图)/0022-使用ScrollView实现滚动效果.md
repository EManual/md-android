ScrollView也是一个Layout布局，可以让它内部的数据显示不下的时候出现滚动条，要注意的是不能在ScrollView中放多个组件，如果放了多个组件，会出现如下错误：ERROR/AndroidRuntime(271): Caused by: java.lang.IllegalStateException: ScrollView can host only one direct child （ScrollView只能包裹一个直接子元素）
我们看一个例子：
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android" 
    android:id="@+id/ScrollView01" 
    android:layout_width="wrap_content" 
    android:layout_height="wrap_content" >
    <TableLayout
        xmlns:android="http://schemas.android.com/apk/res/android" 
        android:id="@+id/TableLayout01" 
        android:layout_width="fill_parent" 
        android:layout_height="fill_parent" 
        android:stretchColumns="0" >
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:layout_gravity="center" 
                android:layout_span="2" 
                android:text="色彩透明度测试" 
                android:textSize="18dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="ff00ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="ff00ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="ee00ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="ee00ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="dd00ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="dd00ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="cc00ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="cc00ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="bb00ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="bb00ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="aa00ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="aa00ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="9900ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="9900ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="8800ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="8800ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="7700ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="7700ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="6600ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="6600ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="5500ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="5500ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="4400ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="4400ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="3300ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="3300ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="2200ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="2200ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="1100ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="1100ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TableRow
            android:layout_width="fill_parent" 
            android:layout_height="30dip" >
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="0000ff00" >
            </TextView>
            <TextView
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" 
                android:background="000" 
                android:text="0000ff00" 
                android:textColor="fff" 
                android:textSize="30dip" >
            </TextView>
        </TableRow>
        <TextView
            android:layout_width="fill_parent" 
            android:layout_height="wrap_content" 
            android:gravity="center_horizontal" 
            android:text="色彩透明度测试" 
            android:textSize="18dip" >
        </TextView>
    </TableLayout>
</ScrollView>
```
例子的显示效果：
![img](P)  
向下滚屏后的截图：
![img](P)  