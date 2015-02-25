今天给大家介绍一下如何实现androd主页面的左右拖动效果。实现起来很简单，就是使用ViewFlipper来将您要来回拖动的View装在一起，然后与GestureDetector手势识别类来联动，确定要显示哪个View，加上一点点动画效果即可。比如当手指向左快速滑动时跳转到上一个View，手指向右快速滑动时跳转到下一个View，本例中使用图片作为各个View的页面，实现左右快速滑动显示不同的图片。
我们来看看ViewFlipper代码。
```  
<linearlayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_vertical" >
    <imageview
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/v1" >
    </imageview>
</linearlayout>
```
我们的Activity需要实现两个接口OnGestureListener,OnTouchListener。
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.View;
import android.view.GestureDetector.OnGestureListener;
import android.view.View.OnTouchListener;
import android.view.animation.AccelerateInterpolator;
import android.view.animation.Animation;
import android.view.animation.TranslateAnimation;
import android.widget.ViewFlipper;
public class ViewFlipperDemo extends Activity implements OnGestureListener,
		OnTouchListener {
	private ViewFlipper mFlipper;
	GestureDetector mGestureDetector;
	private int mCurrentLayoutState;
	private static final int FLING_MIN_DISTANCE = 100;
	private static final int FLING_MIN_VELOCITY = 200;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mFlipper = (ViewFlipper) findViewById(R.id.flipper);
		// 注册一个用于手势识别的类
		mGestureDetector = new GestureDetector(this);
		// 给mFlipper设置一个listener
		mFlipper.setOnTouchListener(this);
		mCurrentLayoutState = 0;
		// 允许长按住ViewFlipper,这样才能识别拖动等手势
		mFlipper.setLongClickable(true);
	}
	[Tags]/**
	 [Tags]* 此方法在本例中未用到，可以指定跳转到某个页面
	 [Tags]* @param switchTo
	 [Tags]*/
	public void switchLayoutStateTo(int switchTo) {
		while (mCurrentLayoutState != switchTo) {
			if (mCurrentLayoutState > switchTo) {
				mCurrentLayoutState--;
				mFlipper.setInAnimation(inFromLeftAnimation());
				mFlipper.setOutAnimation(outToRightAnimation());
				mFlipper.showPrevious();
			} else {
				mCurrentLayoutState++;
				mFlipper.setInAnimation(inFromRightAnimation());
				mFlipper.setOutAnimation(outToLeftAnimation());
				mFlipper.showNext();
			}
		};
	}
	[Tags]/**
	 [Tags]* 定义从右侧进入的动画效果
	 [Tags]*/
	protected Animation inFromRightAnimation() {
		Animation inFromRight = new TranslateAnimation(
		Animation.RELATIVE_TO_PARENT, +1.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f);
		inFromRight.setDuration(500);
		inFromRight.setInterpolator(new AccelerateInterpolator());
		return inFromRight;
	}
	[Tags]/**
	 [Tags]* 定义从左侧退出的动画效果
	 [Tags]*/
	protected Animation outToLeftAnimation() {
		Animation outtoLeft = new TranslateAnimation(
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, -1.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f);
		outtoLeft.setDuration(500);
		outtoLeft.setInterpolator(new AccelerateInterpolator());
		return outtoLeft;
	}
	[Tags]/**
	 [Tags]* 定义从左侧进入的动画效果
	 [Tags]*/
	protected Animation inFromLeftAnimation() {
		Animation inFromLeft = new TranslateAnimation(
		Animation.RELATIVE_TO_PARENT, -1.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f);
		inFromLeft.setDuration(500);
		inFromLeft.setInterpolator(new AccelerateInterpolator());
		return inFromLeft;
	}
	[Tags]/**
	 [Tags]* 定义从右侧退出时的动画效果
	 [Tags]*/
	protected Animation outToRightAnimation() {
		Animation outtoRight = new TranslateAnimation(
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, +1.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f,
		Animation.RELATIVE_TO_PARENT, 0.0f);
		outtoRight.setDuration(500);
		outtoRight.setInterpolator(new AccelerateInterpolator());
		return outtoRight;
	}
	public boolean onDown(MotionEvent e) {
		return false;
	}
	/*
	 [Tags]* 用户按下触摸屏、快速移动后松开即触发这个事件
	 [Tags]* e1：第1个ACTION_DOWN MotionEvent
	 [Tags]* e2：最后一个ACTION_MOVE MotionEvent
	 [Tags]* velocityX：X轴上的移动速度，像素/秒
	 [Tags]* velocityY：Y轴上的移动速度，像素/秒
	 [Tags]* 触发条件 ：
	 [Tags]* X轴的坐标位移大于FLING_MIN_DISTANCE，且移动速度大于FLING_MIN_VELOCITY个像素/秒
	 [Tags]*/
	public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
	float velocityY) {
		if (e1.getX() - e2.getX() > FLING_MIN_DISTANCE
		&& Math.abs(velocityX) > FLING_MIN_VELOCITY) {
			// 当像左侧滑动的时候
			// 设置View进入屏幕时候使用的动画
			mFlipper.setInAnimation(inFromRightAnimation());
			// 设置View退出屏幕时候使用的动画
			mFlipper.setOutAnimation(outToLeftAnimation());
			mFlipper.showNext();
		} else if (e2.getX() - e1.getX() > FLING_MIN_DISTANCE
		&& Math.abs(velocityX) > FLING_MIN_VELOCITY) {
			// 当像右侧滑动的时候
			mFlipper.setInAnimation(inFromLeftAnimation());
			mFlipper.setOutAnimation(outToRightAnimation());
			mFlipper.showPrevious();
		}
		return false;
	}
	public void onLongPress(MotionEvent e) {
	}
	public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX,
	float distanceY) {
		return false;
	}

	public void onShowPress(MotionEvent e) {
	}

	public boolean onSingleTapUp(MotionEvent e) {
		return false;
	}
	public boolean onTouch(View v, MotionEvent event) {
		// 一定要将触屏事件交给手势识别类去处理（自己处理会很麻烦的）
		return mGestureDetector.onTouchEvent(event);
	}
}
```