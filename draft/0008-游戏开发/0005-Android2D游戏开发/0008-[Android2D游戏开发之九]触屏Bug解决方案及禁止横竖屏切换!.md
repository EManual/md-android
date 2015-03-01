我们先讲解在触屏事件处理中我们需要改进的bug！然后再给出如何禁止横屏和竖屏切换!以及切换的过程在android os中是怎样的。
先看一段代码：
```  
@Override  
public boolean onTouchEvent(MotionEvent event) {  
	Log.v("test", "onTouchEvent");
	bmp_y++;
	if (event.getAction() == MotionEvent.ACTION_MOVE) {  
		Log.v("android", "ACTION_MOVE");
	} else if (event.getAction() == MotionEvent.ACTION_DOWN) {  
		Log.v("android", "ACTION_DOWN");
	} else if (event.getAction() == MotionEvent.ACTION_UP) {  
		Log.v("android", "ACTION_UP");
	}  
	return true;  
	//return super.onTouchEvent(event);//备注1  
}  
public boolean onKeyDown(int keyCode, KeyEvent event) {  
	Log.v("test", "onKeyDown");
	bmp_x++;
	return super.onKeyDown(keyCode, event);  
}
```
代码很简单，一个是处理实体按键的响应时间，另一个是触屏的响应事件、那么这里要说的有两点：
第一点：在surfaceview中我们的onKeyDown虽然是重写了view的函数，但是仍然需要在初始化的时候去声明获取焦点，setFocusable(true);如果不调用此方法，那么会造成按键无效。原因是因为如果是自己定义一个继承自View的类,重新实现onKeyDown方法后,只有当该View获得焦点时才会调用onKeyDown方法,Actvity中的onKeyDown方法是当所有控件均没有处理该按键事件时,才会调用。
第二点：也是今天主要需要讲得的触屏响应的函数，onTouchEvent()！重写此函数的时候默认最后一句是依照基类的返回方式，return super.onTouchEvent(event);
然后我们在其中去判定MotionEvent.ACTION_MOVE、MotionEvent.ACTION_DOWN、MotionEvent.ACTION_UP 相对应触屏操作的拖动、按下、抬起；
对此一切都是正确的，但是真正的的运行起项目的时候发现Log.v("android","ACTION_MOVE");
这里log的"ACTION_MOVE"，永远不会执行！！！为此我找到了解决方法，那么先解释下为什么会出现此类情况。
解释：onTouchEvent()，预设使用Oeverride这个方法，通常情況下去呼叫super.onTouchEvent()并传回布林值。但是这里要注意一点，预设如果去呼叫super.onTouchEvent()則很有可能super里面并没做任何事，并且回传false回來，一旦回传false回來，很可能后面的event(例如：Action_Move、Action_Up)都会收不到了，所以为了确保保后面event能順利收到，要注意是否要直接呼super.TouchEvent()。
例如：
```  
@Override  
public boolean onTouchEvent(MotionEvent event) {  
	Log.i("ConanLog", "Event"+event.getAction());
	return super.onTouchEvent(event);  
}  
```
这个例子是当你Touch Down的时候会送event進來，接著印出Log，然后呼叫super的onTouchEvent()并回传布林值。此时会回传false，并且之后再也收不到Touch Move或Touch Up的event，為了要确保能收到event，必須要回传true，所以在这里要注意一下。这个问题也是当时用到此函数的时候发现的，找了很多资料才找到其解释、所以以后使用onTouchEvent（）函数的时候最后的 return super.onTouchEvent(event);一定要改 return true;最后还要注意一点：在初始化的时候不要忘记setFocusableInTouchMode（true）;触屏模式获取焦点，比较类似setFocusable(true);——setFocusable(true);//此方法是用来响应按键！如果是自己定义一个继承自View的类，重新实现onKeyDown方法后，只有当该View获得焦点时才会调用onKeyDown方法，Actvity中的onKeyDown方法是当所有控件均没有处理该按键事件时，才会调用。                                     
这里讲下如何禁止横屏和竖屏切换!
在某些游戏中我们可能需要禁止横屏和竖屏切换，其实实现这个要求很简单，只要在AndroidManifest.xml里面加入这一行android:screenOrientation="landscape"（landscape是横向，portrait是纵向）。在android中每次屏幕的切换动会重启Activity，所以应该在Activity销毁前保存当前活动的状态，在Activity再次Create的时候载入配置。在activity加上android:configChanges="keyboardHidden|orientation"属性,就不会重启activity.而是去调用onConfigurationChanged(Configuration newConfig). 这样就可以在这个方法里调整显示方式. MainActivity中：
```  
public void onConfigurationChanged(Configuration newConfig) {  
	try {  
		super.onConfigurationChanged(newConfig);  
		if (this.getResources().getConfiguration().orientation == Configuration.ORIENTATION_LANDSCAPE) {  
			Log.v("android", "onConfigurationChanged_ORIENTATION_LANDSCAPE");
		} else if (this.getResources().getConfiguration().orientation == Configuration.ORIENTATION_PORTRAIT) {  
			Log.v("android", "onConfigurationChanged_ORIENTATION_PORTRAIT");
		}  
	} catch (Exception ex) {  
	}  
} 
```
AndroidManifest.xml中：
```  
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
    package="com.android" android:versionCode="1" android:versionName="1.0">
    <application android:icon="@drawable/icon" android:label="@string/app_name">
        <activity android:name=".MainActivity" android:label="@string/app_name"
            android:screenOrientation="landscape" android:configChanges="keyboardHidden|orientation">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="4" />
</manifest>  
```