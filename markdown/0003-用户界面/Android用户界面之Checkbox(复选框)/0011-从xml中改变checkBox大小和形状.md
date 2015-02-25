主程序很简单了
setContentView(R.layout.main);
看一下main
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <CheckBox
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="a checkbox" />
    <CheckBox
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true
        android:text="checked checkbox" />
    <CheckBox
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/checkbox_background"
        android:button="@drawable/checkbox"
        android:text="new checkbox" />
    <CheckBox
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/checkbox_background"
        android:button="@drawable/checkbox"
        android:checked="true
        android:text="new checked checkbox" />
</LinearLayout> 
```