Android作为一个新兴的手机智能操作系统已经越来越热门，关于Android平台下的开发也是五花八门，我们这个篇主要讲的就是android屏幕保护的一个应用，希望大家多多的支持，希望武林高手多多的指点我代码中的不足，这样我才能更好的进步。
代码的思路大致是这样的：首先有一个Service，当然这个Service在主Activity中启动，在Service中注册一个receiver,该receiver监听系统的ScreenOff（即屏幕关闭）事件，当然在Service中要关闭原有的屏保(关闭系统屏保需要再配置文件中获得权限)。然后在onReceive方法中启动自己的屏保Activity。有一点需要注意到得是Screen off 事件不能在AndroidManifest.xml配置文件中注册。
Service：
这两个变量主要是为了关闭系统原有屏保，下面将用到
```  
KeyguardManager mKeyguardManager=null; 
private KeyguardLock mKeyguardLock=null; 
```
关闭系统屏保：
```  
mKeyguardManager= (KeyguardManager)getSystemService(Context.KEYGUARD_SERVICE);
mKeyguardLock= mKeyguardManager.newKeyguardLock("");
mKeyguardLock.disableKeyguard();
```
注册receiver：
```  
BroadcastReceiver mMasterResetReciever = new BroadcastReceiver() {
	public void onReceive(Context context, Intent intent) {
		try {
			Intent i = new Intent();
			i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
			i.setClass(context, MyScreenSaver.class);
			context.startActivity(i);
			// finish();

		} catch (Exception e) {
			Log.i("Output:", e.toString());
		}
	}
};
registerReceiver(mMasterResetReciever, new IntentFilter(
		Intent.ACTION_SCREEN_OFF));
```
可以看到在receiver的onReceive()函数中启动一个屏保Activity。
之后我们需要再配置文件中申请权限：
```  
<uses-permission android:name="android.permission.DISABLE_KEYGUARD"></uses-permission>
```
将Activity全屏显示的方法：
```  
requestWindowFeature(Window.FEATURE_NO_TITLE);
getWindow().setFlags
(
	WindowManager.LayoutParams.FLAG_FULLSCREEN,
	WindowManager.LayoutParams.FLAG_FULLSCREEN
);
setContentView(R.layout.main);
```
注意：
setContentView(R.layout.main);全屏代码之后，否则无效
任意键关闭屏保Activity可以通过重写onKeyDown()函数来实现：
```  
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
	super.onKeyDown(keyCode, event);
	finish();
	return true;
}
```