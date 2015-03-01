XML如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout1"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/black" >
    <Button
        android:id="@+id/button1"
        android:layout_width="118px"
        android:layout_height="wrap_content"
        android:text="Go to Layout2" />
    <TextView
        android:id="@+id/text1"
        android:layout_width="186px"
        android:layout_height="29px"
        android:layout_below="@+id/button1"
        android:text="@string/layout1"
        android:textSize="24sp" />
</RelativeLayout>
```
XML如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout2"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/white" >
    <Button
        android:id="@+id/button2"
        android:layout_width="118px"
        android:layout_height="wrap_content"
        android:text="Go to Layout1" >
    </Button>
    <TextView
        android:id="@+id/text2"
        android:layout_width="186px"
        android:layout_height="29px"
        android:layout_below="@+id/button2"
        android:text="@string/layout2"
        android:textColor="@drawable/black"
        android:textSize="24sp" >
    </TextView>
</RelativeLayout>
```