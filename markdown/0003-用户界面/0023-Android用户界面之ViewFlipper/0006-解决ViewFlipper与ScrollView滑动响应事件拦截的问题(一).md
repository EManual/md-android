最近在做一个简单的展示界面时，遇到了一个比较棘手的问题。由于要展示多项内容，所以使用ViewFlipper作为水平滑动容器；而每项内容中由于许多文本较长，因此需要使用ScrollView作为垂直滑动容器。基本的界面布局大致如下：
外部文件common_list_view.xml：
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/geyan_query_view_layout"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/mid_bg"
    android:orientation="vertical" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_gravity="top"
        android:layout_marginTop="43dip"
        android:gravity="top"
        android:orientation="vertical" >
        <Gallery
            android:id="@+id/gallery_data"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:layout_gravity="top"
            android:gravity="top"
            android:paddingLeft="6dip"
            android:paddingRight="6dip"
            android:spacing="60dip" >
        </Gallery>
    </LinearLayout>
    <ImageView
        android:id="@+id/main_background"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
    <include
        android:id="@+id/title"
        layout="@layout/common_title_view" />
</RelativeLayout> 
```
内部文件common_info_view.xml：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/linear"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/text_title"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dip"
        android:gravity="center"
        android:padding="5dip"
        android:textColor="181712"
        android:textSize="20sp"
        android:textStyle="bold" />
    <ScrollView
        android:id="@+id/scroll"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_marginBottom="5dip"
        android:fadeScrollbars="true" >
        <TextView
            android:id="@+id/text_detail"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:lineSpacingMultiplier="1.3"
            android:singleLine="false"
            android:textColor="181712"
            android:textSize="18sp" />
    </ScrollView>
</LinearLayout> 
```