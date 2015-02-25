GridView是一项显示二维的viewgroup，可滚动的网格。一般用来显示多张图片。
以下模拟九宫图的实现，当鼠标点击图片时会进行相应的跳转链接。
main.xml布局文件，存放GridView控件
```  
<?xml version="1.0" encoding="utf-8"?>
<!--
	android:numColumns="auto_fit" ，GridView的列数设置为自动
	android:columnWidth="90dp"，每列的宽度，也就是Item的宽度
	android:stretchMode="columnWidth"，缩放与列宽大小同步
	android:verticalSpacing="10dp"，两行之间的边距，如：行一(NO.0~NO.2)与行二(NO.3~NO.5)间距为10dp
	android:horizontalSpacing="10dp"，两列之间的边距
-->
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:gravity="center"
    android:horizontalSpacing="10dp"
    android:numColumns="auto_fit"
    android:stretchMode="columnWidth"
    android:verticalSpacing="10dp" />
```
night_item.xml布局文件，存放显示控件
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:paddingBottom="4dip" >
    <ImageView
        android:id="@+id/itemImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true" >
    </ImageView>
    <TextView
        android:id="@+id/itemText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/itemImage"
        android:layout_centerHorizontal="true
        android:text="TextView01" >
    </TextView>
</RelativeLayout>
```
strings.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<string name="hello">Hello World, GvActivity!</string>
	<string name="app_name">九宫图</string>
	<string name="test_name1">跳转到TestActivity1</string>
	<string name="test_name2">跳转到TestActivity2</string>
	<string name="test_name3">跳转到TestActivity3</string>
</resources>
```