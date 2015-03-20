最近由于需要开发一个基于android系统的小查询系统，直接从代码里面学习是最快的，确实通过三天的努力，系统完成了，中间走了不少弯路，但是起码东西是做出来了,有很多代码都是从这个论坛里下的，也没有什么回报的，只能上传一些学习过程中的代码了。
该代码的效果图1如下:
![img](http://emanual.github.io/md-android/img/view_listview/22_listview.jpg) 
main.xml的代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="253853"
    android:orientation="vertical" >
    <ListView
        android:id="@+id/list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginBottom="10dp"
        android:cacheColorHint="00000000"
        android:divider="2A4562"
        android:dividerHeight="4px"
        android:drawSelectorOnTop="false"
        android:listSelector="264365" >
    </ListView>
</LinearLayout>
```
list_item.xml的代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/selector" >
    <ImageView
        android:id="@+id/img"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="20dp" />
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" >
        <TextView
            android:id="@+id/title"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_marginLeft="20dp"
            android:layout_marginTop="20dp"
            android:gravity="center_vertical"
            android:text="data"
            android:textColor="@color/black"
            android:textSize="14sp"
            android:textStyle="bold" >
        </TextView>
    </LinearLayout>
</LinearLayout>
```
selector的代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_selected="true"><shape>
            <gradient android:angle="270" android:endColor="99BD4C" android:startColor="A5D245" />
            <padding android:bottom="20dp" android:left="15dp" android:right="15dp" android:top="20dp" />
            <size android:height="60dp" android:width="320dp" />
            <corners android:radius="8dp" />
        </shape>
    </item>
    <item android:state_pressed="true"><shape>
            <gradient android:angle="270" android:endColor="99BD4C" android:startColor="A5D245" />
            <padding android:bottom="20dp" android:left="15dp" android:right="15dp" android:top="20dp" />
            <size android:height="60dp" android:width="320dp" />
            <corners android:radius="8dp" />
        </shape>
    </item>
    <item>
        <shape>
            <gradient android:angle="270" android:endColor="A8C3B0" android:startColor="C6CFCE" />
            <padding android:bottom="20dp" android:left="15dp" android:right="15dp" android:top="20dp" />
            <size android:height="60dp" android:width="320dp" />
            <corners android:radius="8dp" />
        </shape>
    </item>
</selector>
```
对于只修改一个其中的几个颜色就能实现各种不同的listview风格，肯定比处理图片轻松多了。