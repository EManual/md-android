看到一个老外做的不错的android启动菜单的动画效果，小结下。 
1 首先在drawable目录下放一些动画要用的图片。
2 splash.xml:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android "
android:layout_width="
    android:id="@+id/TheSplashLayout"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    wrap_content="" >
    <ImageView
        android:id="@+id/SplashImageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" >
    </ImageView>
</LinearLayout>
```
3 点启动窗口动画效果后显示的main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" />
<TextView
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="@string/hello" />
```
3 SplashScreen.java
这里是欢迎启动类的核心部分
```  
public class SplashScreen extends Activity {
	[Tags]/**
	 [Tags]* The thread to process splash screen events
	 [Tags]*/
	private Thread mSplashThread;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // Splash screen view
		setContentView(R.layout.splash);
		// Start animating the image
		final ImageView splashImageView = (ImageView) findViewById(R.id.SplashImageView);
		splashImageView.setBackgroundResource(R.drawable.flag);
		final AnimationDrawable frameAnimation = (AnimationDrawable) splashImageView
				.getBackground();
		splashImageView.post(new Runnable() {
			public void run() {
				frameAnimation.start();
			}
		});
		final SplashScreen sPlashScreen = this;
		// The thread to wait for splash screen events
		mSplashThread = new Thread() {
			@Override
			public void run() {
				try {
					synchronized (this) {
						// Wait given period of time or exit on touch
						wait(5000);
					}
				} catch (InterruptedException ex) {
				}
				finish();
				// Run next activity
				Intent intent = new Intent();
				intent.setClass(sPlashScreen, MainActivity.class);
				startActivity(intent);
				stop();
			}
		};
		mSplashThread.start();

	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		super.onCreateOptionsMenu(menu);
		return false;
	}
	[Tags]/**
	 [Tags]* Processes splash screen touch events
	 [Tags]*/
	@Override
	public boolean onTouchEvent(MotionEvent evt) {
		if (evt.getAction() == MotionEvent.ACTION_DOWN) {
			synchronized (mSplashThread) {
				mSplashThread.notifyAll();
			}
		}
		return true;
	}
}
```
4 为了更好看，在values 目录下添加样式文件
styles.xml: 
```  
<resources> 
<style name="Animations" parent="@android:Animation" /> 
<style name="Animations.SplashScreen"> 
    <item name="android:windowEnterAnimation">@anim/appear</item>
    <item name="android:windowExitAnimation">@anim/disappear</item>
</style> 
<style name="Theme.Transparent" parent="android:Theme"> 
	<item name="android:windowIsTranslucent">true</item>
	<item name="android:windowBackground">@android:color/transparent</item>
	<item name="android:windowContentOverlay">@null</item>
	<item name="android:windowNoTitle">true</item>
	<item name="android:windowIsFloating">true</item>
	<item name="android:backgroundDimEnabled">false</item>
	<item name="android:windowAnimationStyle">@style/Animations.SplashScreen</item>
</style>        
</resources> 
注意下这里<style name="Animations" parent="@android:Animation" /> 
```  
<style name="Animations.SplashScreen"> 
    <item name="android:windowEnterAnimation">@anim/appear</item>
    <item name="android:windowExitAnimation">@anim/disappear</item>
</style>
```
效果图：
![img](P)  