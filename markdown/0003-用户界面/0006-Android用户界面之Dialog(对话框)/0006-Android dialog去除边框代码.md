使用样式文件，在values 目录下新建styles.xml文件
```  
<resources>
<style name="dialog" parent="@android:style/Theme.Dialog">
	<item name="android:windowFrame">@null</item>
	<item name="android:windowIsFloating">true</item>
	<item name="android:windowIsTranslucent">false</item>
	<item name="android:windowNoTitle">true</item>
	<item name="android:background">@android:color/black</item>
	<item name="android:windowBackground">@null</item>
	<item name="android:backgroundDimEnabled">false</item>
</style>
</resources>
```
调用时，使用AlerDialog的接口类
```  
Dialog dialog = new Dialog(SetActivity.this, R.style.dialog);
dialog.setContentView(R.layout.test);
dialog.show();
public Dialog(Context context, int theme) {
	mContext = new ContextThemeWrapper(context,
			theme == 0 ? com.android.internal.R.style.Theme_Dialog : theme);
	mWindowManager = (WindowManager) context.getSystemService("window");
	Window w = PolicyManager.makeNewWindow(mContext);
	mWindow = w;
	w.setCallback(this);
	w.setWindowManager(mWindowManager, null, null);
	w.setGravity(Gravity.CENTER);
	mUiThread = Thread.currentThread();
	mDismissCancelHandler = new DismissCancelHandler(this);
}
```
效果图：
![img](P)  
![img](P)  