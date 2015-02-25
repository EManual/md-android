在做Android开发中经常要用到短信的发送和短信的接收，调用Android提供的api实现起来很简单，今天要用到这个功能研究了一下顺便写下来加强一下记忆。
1.首先创建一个Android Project
2.设计一个main Activity作为发短信的操作界面，设计好的界面图如下：
![img](P)  
界面布局的文件内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical"
    android:padding="10sp" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mobile_label" />
    <EditText
        android:id="@+id/txt_from"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:hint="请输入电话号码" />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/content_label" />
    <EditText
        android:id="@+id/txt_content"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:hint="请输入短信内容"
        android:lines="3" />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
    </TextView>
    <Button
        android:id="@+id/btnSend"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:paddingTop="20sp"
        android:text="发送短信" />
</LinearLayout>
```