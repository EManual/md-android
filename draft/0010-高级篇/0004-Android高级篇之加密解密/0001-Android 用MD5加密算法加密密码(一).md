很多的网络相关的软件都需要用户名密码登录，在开发的时候像这些密码都是保存在SharedPreferences中，这些密码保存在/data/data/包名/shared_prefs下，保存在一个XML文件中，如下：
![img](P)  
开始说道正题，MD5加密算法虽然现在有些人已经将其解开了，但是它的加密机制依然很强大，我想绝大对数还是不会解开的。MD5加密算法是单向加密，只能用你的密码才能解开，要不就是会解密算法，否则想都别想解开。为了防止这种情况的发生。还可以对加密过的密码进行再次加密。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <EditText
        android:id="@+id/username"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginRight="10dp"
        android:layout_marginTop="20dp"
        android:hint="帐号" />
    <EditText
        android:id="@+id/password"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginRight="10dp"
        android:layout_marginTop="10dp"
        android:hint="密码"
        android:password="true" />
    <Button
        android:id="@+id/save"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginRight="10dp"
        android:layout_marginTop="10dp"
        android:text="保存" />
    <Button
        android:id="@+id/login"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginRight="10dp"
        android:layout_marginTop="10dp"
        android:text="登录" />
</LinearLayout>
```
#### login.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="login successful!" />
</LinearLayout>
```