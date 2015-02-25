温度传感器Temperature
温度传感器也是应用较多的传感器，通过运用温度传感器便可开发出手机温度计等有趣的应用。本节将对温度传感器进行介绍，使读者掌握该传感器应用的开发步骤。
创建一个名为Sample的Android项目。
准备字符串资源。用下列代码替换strings.xml中原有的代码。
```  
<?xml version="1.0" encoding="utf-8"?>
<!-- 声明XML的版本及编码格式 -->
<resources>
    <string name="hello" >Hello World,Sample
    </string>
    <!-- 定义字符串hello -->
    <string name="app_name" >Sample
    </string>
    <!-- 定义字符串app_name -->
    <string name="title" >温度传感器案例
    </string>
    <!-- 定义字符串title -->
    <string name="myTextView1" >当前的温度为：
    </string>
    <!-- 定义字符串myTextView1 -->
</resources>
```
说明：定义了程序中将会用到的所有字符串资源。
搭建界面。开发main.xml文件，代码如下所示。
```  
<?xml version="1.0" encoding="utf-8"?>
<!-- 声明XML的版本及编码格式 -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!-- 添加一个垂直的线性布局 -->
    <TextView
        android:id="@+id/title"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="@string/title"
        android:textSize="20px" />
    <!-- 添加一个TextView控件 -->
    <TextView
        android:id="@+id/myTextView1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/myTextView1"
        android:textSize="18px" />
    <!-- 添加一个TextView控件 -->
</LinearLayout>
```
说明：首先定义一个垂直的线性布局，然后依次向线性布局中添加两个TextView，并分别指定其ID及其他参数。
为应用程序添加网络权限。将下列代码添加到AndroidManifest.xml文件</manifest>标记之前。如果在真机上调试则不需要加入该权限。
```  
<uses-permission android:name="android.permission.INTERNET"/> <!-- 调试时用 -->
```
为该项目添加JAR包，使其能够使用SensorSimulator工具的API，添加方法与前面各个案例的添加方法完全相同，在此不再赘述。