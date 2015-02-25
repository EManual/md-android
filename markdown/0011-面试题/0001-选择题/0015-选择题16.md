下面在AndroidManifest.xml文件中注册BroadcastReceiver方式正确的()
```  
A、
<receiver android:name="NewBroad">
	<intent-filter>
		<action
		   android:name="android.provider.action.NewBroad"/>
		<action>
	</intent-filter>
</receiver>
B、
<receiver android:name="NewBroad">
	<intent-filter>
		   android:name="android.provider.action.NewBroad"/>
	</intent-filter>
</receiver>
C、
<receiver android:name="NewBroad">
	<action
		  android:name="android.provider.action.NewBroad"/>
	 <action>
</receiver>
D、
<intent-filter>
	<receiver android:name="NewBroad">
		<action>
		   android:name="android.provider.action.NewBroad"/>
		<action>
	</receiver>
</intent-filter>
```
#### 答案：A
