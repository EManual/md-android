1、新建工程：DialogTest
2、编写布局文件：
（1）、main.xml代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <Button
        android:id="@+id/button01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/button01" />
    <Button
        android:id="@+id/button02"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/button02" />
    <Button
        android:id="@+id/button03"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/button03" />
    <Button
        android:id="@+id/button04"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/button04" />
</LinearLayout>
```
（2）alert_dialog_text_entry.xml   此部分布局是实现可以用户交互的。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/username_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:text="用户名
        android:textAppearance="@android:style/TextAppearance.Medium" />
    <EditText
        android:id="@+id/username_edit"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:capitalize="none
        android:textAppearance="@android:style/TextAppearance.Medium" />
    <TextView
        android:id="@+id/password_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:text="密码
        android:textAppearance="@android:style/TextAppearance.Medium" />
    <EditText
        android:id="@+id/password_edit"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:capitalize="none"
        android:password="true
        android:textAppearance="@android:style/TextAppearance.Medium" />
</LinearLayout>
```