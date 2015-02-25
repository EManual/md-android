先简单说说App Widget的原理。App Widget是在桌面上的一块显示信息的东西，通过单击App Widget跳转到程序入口类。而系统自带的程序，典型的App Widget是music，这个Android内置的音乐播放小程序。这个是典型的App Widget+app应用。就是一个程序既可以通过App Widget启动，也可以通过App启动。App Widget就是一个AppWidgetProvider+一个UI界面显示（预先绑定了好多Intent），界面上的信息可以通过程序控制而改变，单击Widget上的控件只能激发发送一个Intent，或发出一个Service的启动通知。而AppWidgetProvider可以拦截这个Intent，而进行相应的处理（比如显示新的信息）。
以下模拟一下App Widget的应用
通过两种方式启动应用程序
1、App Widget启动
长按空白的桌面主屏幕会弹出“添加到主屏幕”，然后选择“窗口小部件”选项进入“选择窗口小部件”，最后选择想要的小部件就会添加到桌面主屏幕，当点击刚才添加的桌面控件就会进入到程序主入口。
2、App启动：跟普通的Activity一样
以下为实现代码
main.xml布局文件，程序入口类的界面
my_layout.xml布局文件：带一个图片的按钮
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="程序入口" />
</LinearLayout>
```
类MainActivity程序入口类
```  
import android.app.Activity;
import android.os.Bundle;
[Tags]/**
 [Tags]* 主程序入口类
 [Tags]*/
public class MainActivity extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
}
```