发送邮件中使用的Intent行为为android.content.Intent.ACTION_SEND。实际上在Android上使用的邮件发送服务是调用Gmail程序，而非直接使用SMTP的Protocol 。现在介绍本篇需要使用到的功能清单：
验证用户输入是否为正确的邮箱格式；
用户可以先把手动输入邮箱，也可以长按邮箱文本框跳到联系人那里找到联系人，得到联系人的邮箱，后返回；
发送邮件。
程序运行的效果图：
![img](P)  
我们现在就来看看项目里的布局代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget34"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/white" >
    <TextView
        android:id="@+id/myTextView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="60px"
        android:layout_y="22px"
        android:text="@string/str_receive" >
    </TextView>
    <TextView
        android:id="@+id/myTextView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="60px"
        android:layout_y="82px"
        android:text="@string/str_cc" >
    </TextView>
    <EditText
        android:id="@+id/myEditText1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_x="120px"
        android:layout_y="12px"
        android:textSize="18sp" >
    </EditText>
    <EditText
        android:id="@+id/myEditText2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_x="120px"
        android:layout_y="72px"
        android:textSize="18sp" >
    </EditText>
    <Button
        android:id="@+id/myButton1"
        android:layout_width="wrap_content"
        android:layout_height="124px"
        android:layout_x="0px"
        android:layout_y="2px"
        android:text="@string/str_button" >
    </Button>
    <TextView
        android:id="@+id/myTextView3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="60px"
        android:layout_y="142px"
        android:text="@string/str_subject" >
    </TextView>
    <EditText
        android:id="@+id/myEditText3"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_x="120px"
        android:layout_y="132px"
        android:textSize="18sp" >
    </EditText>
    <EditText
        android:id="@+id/myEditText4"
        android:layout_width="fill_parent"
        android:layout_height="209px"
        android:layout_x="0px"
        android:layout_y="202px"
        android:textSize="18sp" >
    </EditText>
</AbsoluteLayout>
```