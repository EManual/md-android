本文以android2.2ApiDemo中的例子为题材。下面是显示的结果
![img](P)  
下面的在Activity中onCreate唯一的一行的代码：
setContentView(R.layout.custom_dialog_activity);
custom_dialog_activity：
```  
<TextView xmlns:android="http://schemas.android.com/apk/res/android" android:id="@+id/text" 
	android:layout_width="match_parent" android:layout_height="match_parent"
	android:gravity="center_vertical|center_horizontal"
	android:text="@string/custom_dialog_activity_text"/>
```
android:gravity="center_vertical|center_horizontal" ：表示改TextView中的内容是居中对齐
android:text：指定要求显示的内容文本
下面是androidManifest.xml
```  
<activity android:name=".app.CustomDialogActivity"android:label="@string/activity_custom_dialog" 
android:theme="@style/Theme.CustomDialog"> 
<intent-filter> 
   <action android:name="android.intent.action.MAIN" />
      <category android:name="android.intent.category.LAUNCH" />
</intent-filter> 
</activity>
```
action:MAIN:定义该Activity为切入点，也就是该Activity不接受任何数据。
category:LAUNCH：定义使得该应用程序图标从该Activity进入。
theme：表示设置该Activity的风格，这里是设置为Dialog风格，也就是该Activity将会弹出一个对话框。
具体可以参考:android自带的Theme和Style。