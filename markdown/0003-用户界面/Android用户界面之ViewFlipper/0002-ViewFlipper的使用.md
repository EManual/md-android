屏幕切换指的是在同一个Activity内屏幕见的切换，最长见的情况就是在一个FrameLayout内有多个页面，比如一个系统设置页面；一个个性化设置页面。
通过查看OPhoneAPI文档可以发现，有个android.widget.ViewAnimator类继承至FrameLayout，ViewAnimator类的作用是为FrameLayout里面的View切换提供动画效果。该类有如下几个和动画相关的函数： 
setInAnimation：设置View进入屏幕时候使用的动画，该函数有两个版本，一个接受单个参数，类型为android.view.animation.Animation；一个接受两个参数，类型为Context和int，分别为Context对象和定义Animation的resourceID。 
```  
setOutAnimation: 设置View退出屏幕时候使用的动画，参数setInAnimation函数一样。 
showNext：调用该函数来显示FrameLayout里面的下一个View。 
showPrevious：调用该函数来显示FrameLayout里面的上一个View。 
```
一般不直接使用ViewAnimator而是使用它的两个子类ViewFlipper和ViewSwitcher。ViewFlipper可以用来指定FrameLayout内多个View之间的切换效果，可以一次指定也可以每次切换的时候都指定单独的效果。该类额外提供了如下几个函数： 
```  
isFlipping：用来判断View切换是否正在进行。
setFilpInterval：设置View之间切换的时间间隔。
startFlipping：使用上面设置的时间间隔来开始切换所有的View，切换会循环进行。
stopFlipping: 停止View切换。
```
ViewSwitcher顾名思义Switcher特指在两个View之间切换。可以通过该类指定一个ViewSwitcher.ViewFactory工程类来创建这两个View。该类也具有两个子类ImageSwitcher、TextSwitcher分别用于图片和文本切换。
#### ViewFlipper示例
记住，ViewFlipper是继承至FrameLayout的，所以它是一个Layout里面可以放置多个View。在示例中定义一个ViewFlipper，里面包含三个ViewGroup作为示例的三个屏幕，每个ViewGroup中包含一个按钮和一张图片，点击按钮则显示下一个屏幕。代码如下（res\layout\main.xml）： 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ViewFlipper
        android:id="@+id/details"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:flipInterval="1000"
        android:inAnimation="@anim/push_left_in"
        android:outAnimation="@anim/push_left_out"
        android:persistentDrawingCache="animation" >
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:orientation="vertical" >
            <Button
                android:id="@+id/Button_next1"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:text="Next" >
            </Button>
            <ImageView
                android:id="@+id/image1"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:src="@drawable/dell1" >
            </ImageView>
        </LinearLayout>
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:orientation="vertical" >
            <Button
                android:id="@+id/Button_next2"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:text="Next" >
            </Button>
            <ImageView
                android:id="@+id/image2"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:src="@drawable/lg" >
            </ImageView>
        </LinearLayout>
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:orientation="vertical" >
            <Button
                android:id="@+id/Button_next3"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:text="Next" >
            </Button>
            <ImageView
                android:id="@+id/image3"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:src="@drawable/lenovo" >
            </ImageView>
        </LinearLayout>
    </ViewFlipper>
</LinearLayout> 
```
很简单，在Layout定义中指定动画的相关属性就可以了，通过persistentDrawingCache指定缓存策略；flipInterval指定每个View动画之间的时间间隔；inAnimation和outAnimation分别指定View进出使用的动画效果。动画效果定义如下：
```  
res\anim\push_left_in.xml    
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="500"
        android:fromXDelta="100%p"
        android:toXDelta="0" />
    <alpha
        android:duration="500"
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />
</set>
```
res\anim\push_left_out.xml  
```   
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="500"
        android:fromXDelta="0"
        android:toXDelta="-100%p" />
    <alpha
        android:duration="500"
        android:fromAlpha="1.0"
        android:toAlpha="0.0" />
</set>  
```
Activity代码如下（src\cc\c\TestActivity.java）：
```  
public class TestActivity extends Activity {
	private ViewFlipper mViewFlipper;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button buttonNext1 = (Button) findViewById(R.id.Button_next1);
		mViewFlipper = (ViewFlipper) findViewById(R.id.flipper);
		buttonNext1.setOnClickListener(new View.OnClickListener() {
			public void onClick(View view) {
				// 在layout中定义的属性，也可以在代码中指定
				// mViewFlipper.setInAnimation(getApplicationContext(),
				// R.anim.push_left_in);
				// mViewFlipper.setOutAnimation(getApplicationContext(),
				// R.anim.push_left_out);
				// mViewFlipper.setPersistentDrawingCache(ViewGroup.PERSISTENT_ALL_CACHES);
				// mViewFlipper.setFlipInterval(1000);
				mViewFlipper.showNext();
				// 调用下面的函数将会循环显示mViewFlipper内的所有View。
				// mViewFlipper.startFlipping();
			}
		});
		Button buttonNext2 = (Button) findViewById(R.id.Button_next2);
		buttonNext2.setOnClickListener(new View.OnClickListener() {
			public void onClick(View view) {
				mViewFlipper.showNext();
			}
		});
		Button buttonNext3 = (Button) findViewById(R.id.Button_next3);
		buttonNext3.setOnClickListener(new View.OnClickListener() {
			public void onClick(View view) {
				mViewFlipper.showNext();
			}
		});
	}
}  
```
通过手势移动屏幕
上面是通过屏幕上的按钮来在屏幕间切换的，这看起来多少有点不符合OPhone的风格，如果要是能通过手势的左右滑动来实现屏幕的切换就比较优雅了。 
通过android.view.GestureDetector类可以检测各种手势事件，该类有两个回调接口分别用来通知具体的事件： 
GestureDetector.OnDoubleTapListener：用来通知DoubleTap事件，类似于鼠标的双击事件，该接口有如下三个回调函数： 
1. onDoubleTap(MotionEvent e)：通知DoubleTap手势， 
2. onDoubleTapEvent(MotionEvent e)：通知DoubleTap手势中的事件，包含down、up和move事件（这里指的是在双击之间发生的事件，例如在同一个地方双击会产生DoubleTap手势，而在DoubleTap手势里面还会发生down和up事件，这两个事件由该函数通知）； 
3. onSingleTapConfirmed(MotionEvent e)：用来判定该次点击是SingleTap而不是DoubleTap，如果连续点击两次就是DoubleTap手势，如果只点击一次，OPhone系统等待一段时间后没有收到第二次点击则判定该次点击为SingleTap而不是DoubleTap，然后触发SingleTapConfirmed事件。 
GestureDetector.OnGestureListener：用来通知普通的手势事件，该接口有如下六个回调函数：
```  
onDown(MotionEvent e)：down事件； 
onSingleTapUp(MotionEvent e)：一次点击up事件； 
onShowPress(MotionEvent e)：down事件发生而move或则up还没发生前触发该事件； 
onLongPress(MotionEvent e)：长按事件；
onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY)：滑动手势事件；
onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY)：在屏幕上拖动事件。
```
这些事件有些定义的不太容易理解，在示例项目中实现了所有的回调函数，在每个函数中输出相关的日志，对这些事件不理解的可以运行项目，通过不同的操作来触发事件，然后观看logcat输出日志可有助于对这些事件的理解。 
在上述事件中，如果在程序中处理的该事件就返回true否则返回false，在GestureDetector中也定义了一个SimpleOnGestureListener类，这是个助手类，实现了上述的所有函数并且都返回false。如果在项目中只需要监听某个事件继承这个类可以少些几个空回调函数。 
要走上面的程序中添加滑动手势来实现屏幕切换的话，首先需要定义一个GestureDetector：
```  
private GestureDetector mGestureDetector;
```
并在onCreate函数中初始化：
```  
mGestureDetector = new GestureDetector(this);
```
参数是OnGestureListener，然后让TestActivity实现 OnGestureListener 和OnDoubleTapListener接口：
```  
class TestActivity extends Activity implements OnGestureListener , OnDoubleTapListener
```
然后在onFling函数中实现切换屏幕的功能：
```  
public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
		float velocityY) {
	Log.d(tag, "...onFling...");
	if (e1.getX() > e2.getX()) {// move to left
		mViewFlipper.showNext();
	} else if (e1.getX() < e2.getX()) {
		mViewFlipper.setInAnimation(getApplicationContext(),
				R.anim.push_right_in);
		mViewFlipper.setOutAnimation(getApplicationContext(),
				R.anim.push_right_out);
		mViewFlipper.showPrevious();
		mViewFlipper.setInAnimation(getApplicationContext(),
				R.anim.push_left_in);
		mViewFlipper.setOutAnimation(getApplicationContext(),
				R.anim.push_left_out);
	} else {
		return false;
	}
	return true;
}  
```
这里实现的功能是从右往左滑动则切换到上一个View，从左往右滑动则切换到下一个View，并且使用不同的in、out 动画使切换效果看起来统一一些。
然后在onDoubleTap中实现双击自动切换的效果，再次双击则停止：
```  
public boolean onDoubleTap(MotionEvent e) {
	Log.d(tag, "...onDoubleTap...");
	if (mViewFlipper.isFlipping()) {
		mViewFlipper.stopFlipping();
	} else {
		mViewFlipper.startFlipping();
	}
	return true;
}  
```
到这里手势代码就完成了，现在可以通过左右滑动切换View并且双击可以自动切换View。细心的读者这里可能会发现一个问题，上面在创建mGestureDetector 的时候使用的是如下代码：
```  
mGestureDetector = new GestureDetector(this);
```
这里的参数为OnGestureListener，而且GestureDetector有个函数setOnDoubleTapListener来设置OnDoubleTapListener，在上面的代码中并没有设置OnDoubleTapListener，那么onDoubleTap事件是如何调用的呢？这里的玄机就要去探探 GestureDetector(OnGestureListener l)这个构造函数的源代码了：
```  
public GestureDetector(OnGestureListener listener) {    
	this(null, listener, null);    
} 
```
调用了另外一个构造函数：
```  
public GestureDetector(Context context, OnGestureListener listener,
		Handler handler) {
	if (handler != null) {
		mHandler = new GestureHandler(handler);
	} else {
		mHandler = new GestureHandler();
	}
	mListener = listener;
	if (listener instanceof OnDoubleTapListener) {
		setOnDoubleTapListener((OnDoubleTapListener) listener);
	}
	init(context);
}  
```
注意到listener instanceof OnDoubleTapListener没有？现在明白了吧。