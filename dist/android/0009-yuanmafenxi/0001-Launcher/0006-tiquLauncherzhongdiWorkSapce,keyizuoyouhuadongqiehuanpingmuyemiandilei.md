提取Launcher中的WorkSapce，可以左右滑动切换屏幕页面的类
#### 下载源码
对于Launcher的桌面滑动大家应该都比较熟悉了，最好的体验应该是可以随着手指的滑动而显示不同位置的桌面。
比一般用ViewFlinger+动画所实现的手势切换页面感觉良好多了~~~~
分析了一下Launcher中的WorkSpace，里面有太多的代码我们用不上了（拖拽，长按），把里面的冗余代码去掉得到实现滑动切换屏幕所必需的。
新建一个ScrollLayout类，继承自ViewGroup。
#### 重写onMeasure和onLayout两个方法：
其中onMeasure方法中，得到ScrollLayout的布局方式（一般使用FILL_PARENT），然后再枚举其中所有的子view，设置它们的布局（FILL_PARENT），这样在ScrollLayout之中的每一个子view即为充满屏幕可以滑动显示的其中一页。
在onLayout方法中，横向画出每一个子view，这样所得到的view的高与屏幕高一致，宽度为getChildCount()-1个屏幕宽度的view。
添加一个Scroller来平滑过渡各个页面之间的切换，重写onInterceptTouchEvent和onTouchEvent来响应手指按下划动时所需要捕获的消息，例如划动的速度，划动的距离等。再配合使用scrollBy (int x, int y)方法得到慢速滑动小距离的时候，所需要显示的内容。最后当手指起来时，根据划动的速度与跨度来判断是向左滑动一页还是向右滑动一页，确保每次用户操作结束之后显示的都是整体的一个子view。
ScrollLayout源码：
```  
import android.graphics.Canvas;
import android.util.AttributeSet;
import android.util.Log;
import android.view.MotionEvent;
import android.view.VelocityTracker;
import android.view.View;
import android.view.ViewConfiguration;
import android.view.ViewGroup;
import android.widget.Scroller;
 /**
  *  * 仿Launcher中的WorkSapce，可以左右滑动切换屏幕的类  *
  */
public class ScrollLayout extends ViewGroup {
	private static final String TAG = "ScrollLayout";
	private Scroller mScroller;
	private VelocityTracker mVelocityTracker;
	private int mCurScreen;
	private int mDefaultScreen = 0;
	private static final int TOUCH_STATE_REST = 0;
	private static final int TOUCH_STATE_SCROLLING = 1;
	private static final int SNAP_VELOCITY = 600;
	private int mTouchState = TOUCH_STATE_REST;
	private int mTouchSlop;
	private float mLastMotionX;
	private float mLastMotionY;
	public ScrollLayout(Context context, AttributeSet attrs) {
		this(context, attrs, 0);
	}
	public ScrollLayout(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		mScroller = new Scroller(context);
		mCurScreen = mDefaultScreen;
		mTouchSlop = ViewConfiguration.get(getContext()).getScaledTouchSlop();
	}
	@Override
	protected void onLayout(boolean changed, int l, int t, int r, int b) {
		if (changed) {
			int childLeft = 0;
			final int childCount = getChildCount();
			for (int i = 0; i < childCount; i++) {
				final View childView = getChildAt(i);
				if (childView.getVisibility() != View.GONE) {
					final int childWidth = childView.getMeasuredWidth();
					childView.layout(childLeft, 0, childLeft + childWidth,
							childView.getMeasuredHeight());
					childLeft += childWidth;
				}
			}
		}
	}
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		Log.e(TAG, "onMeasure");
		super.onMeasure(widthMeasureSpec, heightMeasureSpec);
		final int width = MeasureSpec.getSize(widthMeasureSpec);
		final int widthMode = MeasureSpec.getMode(widthMeasureSpec);
		if (widthMode != MeasureSpec.EXACTLY) {
			throw new IllegalStateException(
					"ScrollLayout only canmCurScreen run at EXACTLY mode!");
		}
		final int heightMode = MeasureSpec.getMode(heightMeasureSpec);
		if (heightMode != MeasureSpec.EXACTLY) {
			throw new IllegalStateException(
					"ScrollLayout only can run at EXACTLY mode!");
		}
		// The children are given the same width and height as the scrollLayout
		final int count = getChildCount();
		for (int i = 0; i < count; i++) {
			getChildAt(i).measure(widthMeasureSpec, heightMeasureSpec);
		}
		// Log.e(TAG, "moving to screen "+mCurScreen);
		scrollTo(mCurScreen * width, 0);
	}
	 /**
	  *  * According to the position of current layout  * scroll to the destination page.
	  */
	public void snapToDestination() {
		final int screenWidth = getWidth();
		final int destScreen = (getScrollX() + screenWidth / 2) / screenWidth;
		snapToScreen(destScreen);
	}
	public void snapToScreen(int whichScreen) {
		// get the valid layout page
		whichScreen = Math.max(0, Math.min(whichScreen, getChildCount() - 1));
		if (getScrollX() != (whichScreen * getWidth())) {
			final int delta = whichScreen * getWidth() - getScrollX();
			mScroller.startScroll(getScrollX(), 0, delta, 0,
					Math.abs(delta) * 2);
			mCurScreen = whichScreen;
			invalidate();
			// Redraw the layout
		}
	}
	public void setToScreen(int whichScreen) {
		whichScreen = Math.max(0, Math.min(whichScreen, getChildCount() - 1));
		mCurScreen = whichScreen;
		scrollTo(whichScreen * getWidth(), 0);
	}
	public int getCurScreen() {
		return mCurScreen;
	}
	@Override
	public void computeScroll() {
		if (mScroller.computeScrollOffset()) {
			scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
			postInvalidate();
		}
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		if (mVelocityTracker == null) {
			mVelocityTracker = VelocityTracker.obtain();
		}
		mVelocityTracker.addMovement(event);
		final int action = event.getAction();
		final float x = event.getX();
		final float y = event.getY();
		switch (action) {
		case MotionEvent.ACTION_DOWN:
			Log.e(TAG, "event down!");
			if (!mScroller.isFinished()) {
				mScroller.abortAnimation();
			}
			mLastMotionX = x;
			break;
		case MotionEvent.ACTION_MOVE:
			int deltaX = (int) (mLastMotionX - x);
			mLastMotionX = x;
			scrollBy(deltaX, 0);
			break;
		case MotionEvent.ACTION_UP:
			Log.e(TAG, "event : up");
			//
			if (mTouchState == TOUCH_STATE_SCROLLING) {
				final VelocityTracker velocityTracker = mVelocityTracker;
				velocityTracker.computeCurrentVelocity(1000);
				int velocityX = (int) velocityTracker.getXVelocity();
				Log.e(TAG, "velocityX:" + velocityX);
				if (velocityX > SNAP_VELOCITY && mCurScreen > 0) {
					// Fling enough to move left
				}
			} else if (velocityX < -SNAP_VELOCITY
					&& mCurScreen < getChildCount() - 1) {
				// Fling enough to move right
				Log.e(TAG, "snap right");
				snapToScreen(mCurScreen + 1);
			} else {
				snapToDestination();
			}
			if (mVelocityTracker != null) {
				mVelocityTracker.recycle();
				mVelocityTracker = null;
			}
			mTouchState = TOUCH_STATE_REST;
			break;
		case MotionEvent.ACTION_CANCEL:
			mTouchState = TOUCH_STATE_REST;
			break;
			return true;
		}
	}
	@Override
	public boolean onInterceptTouchEvent(MotionEvent ev) {
		Log.e(TAG, "onInterceptTouchEvent-slop:" + mTouchSlop);
		final int action = ev.getAction();
		if ((action == MotionEvent.ACTION_MOVE)
				&& (mTouchState != TOUCH_STATE_REST)) {
			return true;
		}
		final float x = ev.getX();
		final float y = ev.getY();
		switch (action) {
		case MotionEvent.ACTION_MOVE:
			final int xDiff = (int) Math.abs(mLastMotionX - x);
			if (xDiff > mTouchSlop) {
				mTouchState = TOUCH_STATE_SCROLLING;
			}
			break;
		case MotionEvent.ACTION_DOWN:
			mLastMotionX = x;
			mLastMotionY = y;
			mTouchState = mScroller.isFinished() ? TOUCH_STATE_REST
					: TOUCH_STATE_SCROLLING;
			break;
		case MotionEvent.ACTION_CANCEL:
		case MotionEvent.ACTION_UP:
			mTouchState = TOUCH_STATE_REST;
			break;
		}
		return mTouchState != TOUCH_STATE_REST;
	}
}
```
测试程序布局：
```  
<?xml version="1.0" encoding="utf-8"?>
<com.yao_guet.test.ScrollLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/ScrollLayoutTest"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:layout_width="fill_parentv
        android:layout_height="fill_parent"
        android:background="FF00" >
    </LinearLayout>
    <FrameLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:background="F0F0" >
    </FrameLayout>
    <FrameLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:background="F00F" >
    </FrameLayout>
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:background="FF00" >
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Button1" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" >
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Button2" />
    </LinearLayout>
</com.yao_guet.test.ScrollLayout>
```