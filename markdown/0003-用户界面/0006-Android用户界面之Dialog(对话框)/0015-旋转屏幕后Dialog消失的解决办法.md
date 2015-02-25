旋转屏幕后总会出现这样那样的bug问题，现在我们来解决一个简单的吧～
当旋转屏幕（纵－>横）后，Activity会重新调用onCreate()方法，所以，我们不让他调用就好了，在AndroidManifest.xml里面，<activiity></activity>标签里面，加入android:configChanges="orientation|keyboardHidden" 这句话，就ok了。
即：
```  
<activity 
	android:name="Settings" 
	android:label="@string/settings_label"
	android:configChanges="orientation|keyboardHidden">
	<intent-filter>
		<action android:name="android.intent.action.VIEW" />
		<action android:name="android.intent.action.MAIN" />
	</intent-filter>
</activity>
```