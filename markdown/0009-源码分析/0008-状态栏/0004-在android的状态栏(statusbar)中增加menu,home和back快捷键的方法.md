由于完全改了status bar，建议先做几张png图片，加到Frameworks/base/core/res/res/drawable下。最好做一张背景图，替换statusbar_background.png
另外我又加了几张icon，分别是home menu和back的正常和按下状态。
这些图片为：
```  
frameworks\base\core\res\res\drawable\ic_menu_back_pressed.png
frameworks\base\core\res\res\drawable\ic_menu_home_pressed.png
frameworks\base\core\res\res\drawable\ic_menu_more_pressed.png
frameworks\base\core\res\res\drawable\ic_volume_down_pressed.png
frameworks\base\core\res\res\drawable\ic_volume_up_pressed.png
frameworks\base\core\res\res\drawable\ic_menu_back.png
frameworks\base\core\res\res\drawable\ic_menu_home.png
frameworks\base\core\res\res\drawable\ic_menu_more.png
frameworks\base\core\res\res\drawable\ic_volume_down.png
frameworks\base\core\res\res\drawable\ic_volume_up.png
```
修改步骤为：
#### 一．修改xml界面
#### 1.创建按钮
```  
frameworks\base\core\res\res\drawable\btn_sbicon_back.xml
frameworks\base\core\res\res\drawable\btn_sbicon_home.xml
frameworks\base\core\res\res\drawable\btn_sbicon_menu.xml
frameworks\base\core\res\res\drawable\btn_sbicon_vol_down.xml
frameworks\base\core\res\res\drawable\btn_sbicon_vol_up.xml
```
基结构如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android ">
    <item android:drawable="@drawable/ic_menu_back_pressed" android:state_pressed="true"/>
    <item android:drawable="@drawable/ic_menu_back" android:state_pressed="false"/>
</selector>
```
#### 2. 增加图标
更改整个status bar，我的方法是：
修改status bar的layerout文件：
Frameworks/base/core/res/res/layout/status_bar.xml
在原来的linearlayout中新增 image view
```  
<?xml version="1.0" encoding="utf-8"?>
<com.android.server.status.StatusBarView xmlns:android="http://schemas.android.com/apk/res/android "
    android:background="@drawable/statusbar_background"
    android:descendantFocusability="afterDescendants"
    android:focusable="true"
    android:orientation="vertical" >
    <LinearLayout
        android:id="@+id/keys"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:orientation="horizontal" >
        <ImageView
            android:id="@+id/status_home"
            android:layout_width="40dip"
            android:layout_height="40dip"
            android:layout_gravity="top"
            android:clickable="true"
            android:paddingLeft="1dip"
            android:paddingRight="1dip"
            android:paddingTop="1dip"
            android:src="@drawable/btn_sbicon_home" />
        <ImageView
            android:id="@+id/status_back"
            android:layout_width="40dip"
            android:layout_height="40dip"
            android:layout_gravity="top"
            android:clickable="true"
            android:paddingLeft="1dip"
            android:paddingRight="1dip"
            android:paddingTop="1dip"
            android:src="@drawable/btn_sbicon_back" />
        <ImageView
            android:id="@+id/status_menu"
            android:layout_width="40dip"
            android:layout_height="40dip"
            android:layout_gravity="top"
            android:clickable="true"
            android:paddingLeft="1dip"
            android:paddingRight="1dip"
            android:paddingTop="1dip"
            android:src="@drawable/btn_sbicon_menu" />
        <ImageView
            android:id="@+id/status_vol_down"
            android:layout_width="40dip"
            android:layout_height="40dip"
            android:layout_gravity="top"
            android:clickable="true"
            android:paddingLeft="1dip"
            android:paddingRight="1dip"
            android:paddingTop="1dip"
            android:src="@drawable/btn_sbicon_vol_down" />
        <ImageView
            android:id="@+id/status_vol_up"
            android:layout_width="40dip"
            android:layout_height="40dip"
            android:layout_gravity="top"
            android:clickable="true"
            android:paddingLeft="1dip"
            android:paddingRight="1dip"
            android:paddingTop="1dip"
            android:src="@drawable/btn_sbicon_vol_up" />
    </LinearLayout>
</com.android.server.status.StatusBarView>
```
这样做的好处就是简单。同时保证home、menu、back按钮，不受它本来的约束。这样status bar上即可看到这些按钮了。
图标的位置，可通过修改 paddingRight， paddingLeft 和paddingTop的值达到最佳视觉效果。
#### 3. 修改status bar的高度。
既然要在status bar上增加那么几个按钮，当然是想要使用触摸操作的，android自带的status bar高度太小，不适用。对于7寸屏的话，50pixel的高度应该是差不多了。
修改高度很简单，修改frameworks/base/core/res/res/values/dimens.xml的status_bar_height属性
```  
<!-- Height of the status bar -->
<dimen name="status_bar_height">50dip</dimen>
```
也可以更改状态栏Icon的大小，frameworks\base\core\res\res\layout\status_bar_icon.xml
```  
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android " 
	android:layout_width="25dp"
	android:layout_height="25dp" >
```
当然，如果相改title的高度，可以修改 Frameworks/base/core/res/res/values/themes.xml中的Window attributes的windowTitleSize值
编译运行一下：
```  
~/donut$ source ./env.sh   
~/donut$ make –j8   
~/donut$ emulator –skin WVGA800 
~/donut$ source ./env.sh
~/donut$ make –j8
~/donut$ emulator –skin WVGA800
```
看状态栏是不是改变了？
#### 二 为按钮添加模拟按键
修改frameworks\base\services\java\com\android\server\status\StatusBarView.java
#### 1.添加各个图片按钮的引用，
```  
android.widget.LinearLayout keysLayout;
android.widget.ImageView btnHome;
android.widget.ImageView btnBack;
android.widget.ImageView btnMenu;
android.widget.ImageView btnVolUp;
android.widget.ImageView btnVolDown;
```
#### 2.修改onFinishInflate()函数，各个图片ID在上面的status_bar.xml中已经定义
```  
@Override
protected void onFinishInflate() {
	/* Begin : Added by TigerPan */
	keysLayout = (android.widget.LinearLayout) findViewById(R.id.keys);
	btnHome = (android.widget.ImageView) findViewById(R.id.status_home);
	btnBack = (android.widget.ImageView) findViewById(R.id.status_back);
	btnMenu = (android.widget.ImageView) findViewById(R.id.status_menu);
	btnVolUp = (android.widget.ImageView) findViewById(R.id.status_vol_up);
	btnVolDown = (android.widget.ImageView) findViewById(R.id.status_vol_down);
	btnHome.setOnClickListener(mKeysListener);
	btnBack.setOnClickListener(mKeysListener);
	btnMenu.setOnClickListener(mKeysListener);
	btnVolUp.setOnClickListener(mKeysListener);
	btnVolDown.setOnClickListener(mKeysListener);
}
```
#### 3.添加各个按钮的事件监听Listener
```  
android.view.View.OnClickListener mKeysListener = new android.view.View.OnClickListener() {
	public void onClick(View v) {
		switch (v.getId()) {
		case R.id.status_home:
			mKeysHandler.sendEmptyMessage(KEY_HOME);
			break;
		case R.id.status_back:
			mKeysHandler.sendEmptyMessage(KEY_BACK);
			break;
		case R.id.status_menu:
			mKeysHandler.sendEmptyMessage(KEY_MENU);
			break;
		case R.id.status_vol_up:
			mKeysHandler.sendEmptyMessage(KEY_VOL_UP);
			break;
		case R.id.status_vol_down:
			mKeysHandler.sendEmptyMessage(KEY_VOL_DOWN);
			break;
		default:
			break;
		}
	}
};
```
#### 4.添加模拟按键处理
```  
private static final int KEY_HOME = 1000;
private static final int KEY_BACK = 1001;
private static final int KEY_MENU = 1002;
private static final int KEY_VOL_UP = 1003;
private static final int KEY_VOL_DOWN = 1004;
private Handler mKeysHandler = new Handler() {
	public void handleMessage(Message msg) {
		switch (msg.what) {
		case KEY_HOME:
			sendKey(KeyEvent.KEYCODE_HOME);
			break;
		case KEY_BACK:
			sendKey(KeyEvent.KEYCODE_BACK);
			break;
		case KEY_MENU:
			sendKey(KeyEvent.KEYCODE_MENU);
			break;
		case KEY_VOL_UP:
			((android.media.AudioManager) mContext
					.getSystemService(Context.AUDIO_SERVICE)).adjustVolume(
					android.media.AudioManager.ADJUST_RAISE,
					android.media.AudioManager.STREAM_MUSIC);
			break;
		case KEY_VOL_DOWN:
			((android.media.AudioManager) mContext
					.getSystemService(Context.AUDIO_SERVICE)).adjustVolume(
					android.media.AudioManager.ADJUST_LOWER,
					android.media.AudioManager.STREAM_MUSIC);
			break;
		default:
			break;
		}
	}
	private void sendKey(int keyCode) {
		long now = SystemClock.uptimeMillis();
		long n = System.currentTimeMillis();
		Log.d("Tiger", "Intent.ACTION_SOFT_" + keyCode + "_PRESSED   0=
				+ n);
		try {
			KeyEvent down = new KeyEvent(now, now, KeyEvent.ACTION_DOWN,
					keyCode, 0);
			KeyEvent up = new KeyEvent(now, now, KeyEvent.ACTION_UP,
					keyCode, 0);
			Log.d("Tiger", "Intent.ACTION_SOFT_" + keyCode
					+ "_PRESSED   1=
					+ (System.currentTimeMillis()/*-n*/));
			IWindowManager wm = IWindowManager.Stub
					.asInterface(ServiceManager.getService("window"));
			Log.d("Tiger", "Intent.ACTION_SOFT_" + keyCode
					+ "_PRESSED   2=
					+ (System.currentTimeMillis()/*-n*/));
			wm.injectKeyEvent(down, false);
			Log.d("Tiger", "Intent.ACTION_SOFT_" + keyCode
					+ "_PRESSED   3=
					+ (System.currentTimeMillis()/*-n*/));
			wm.injectKeyEvent(up, false);
			Log.d("Tiger", "Intent.ACTION_SOFT_" + keyCode
					+ "_PRESSED   4=
					+ (System.currentTimeMillis()/*-n*/));
		} catch (RemoteException e) {
			Log.i("Input", "DeadOjbectException");
		}
	}
};
```
#### 5.避免在按下这几个按钮时，触发下拉Notification视图，影响性能
修改onInterceptTouchEvent(MotionEvent event)函数
```  
@Override
public boolean onInterceptTouchEvent(MotionEvent event) {
	if (keysLayout.getRight() < event.getX())
		return mService.interceptTouchEvent(event) ? true : super
				.onInterceptTouchEvent(event);
	/*
	 [Tags]* int parentLeft = mStatusIcons.getLeft();
	 [Tags]* android.util.Log.i("Tiger","....."+(event.getX() < (parentLeft +
	 [Tags]* iconVolDown.getLeft()) || event.getX() > (parentLeft +
	 [Tags]* iconHome.getRight()))); if(event.getX() < (parentLeft +
	 [Tags]* iconVolDown.getLeft()) || event.getX() > (parentLeft +
	 [Tags]* iconHome.getRight())) return mService.interceptTouchEvent(event) ?
	 [Tags]* true : super.onInterceptTouchEvent(event);
	 [Tags]*/
	return false;
}
```
这样，基本上就完成了。
编译一下
```  
~/donut$ source ./env.sh   
~/donut$ make update-api   
~/donut$ make –j8   
~/donut$ emulator –skin WVGA800 
~/donut$ source ./env.sh
~/donut$ make update-api
~/donut$ make –j8
~/donut$ emulator –skin WVGA800
```