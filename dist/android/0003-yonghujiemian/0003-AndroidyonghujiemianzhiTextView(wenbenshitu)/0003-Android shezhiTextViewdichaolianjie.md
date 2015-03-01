Android TextView URL 、电话与电邮超链接设置，android textview 控件，android textview 超链接，android textview 电话，android textview 电子邮件，android textview web，android textview phone，android textview email，android textview url。
android：autoLink
设置是否当文本为 URL链接、Email、电话号码、Map时，文本显示为可点击的链接。
可选值：none / web / email / phone / map / all 。
1、网页超链接设置
main.xml 文件
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"android:orientation="vertical"
	android:layout_width="fill_parent"
	android:layout_height="fill_parent">
	<TextViewandroid:autoLink="all|web"
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		android:text=""/>
</LinearLayout>
```
2、电话超链接设置
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android=http://schemas.android.com/apk/res/android"
	android:orientation="vertical"
	android:layout_width="fill_parent"
	android:layout_height="fill_parent">
	<TextViewandroid:autoLink="all|phone"
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		android:text=""/>
</LinearLayout>
```