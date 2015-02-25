在android上要实现类似Launch的抽屉效果，大家一定首先会想起SlidingDrawer。SlidingDrawer是android官方控件之一，本文的主角不是它，而是民间的控件工具集合~~~android-misc-widgets。
android-misc-widgets里面包含几个widget：Panel、SmoothButton、Switcher、VirtualKeyboard，还有一些动画特效，本文主要介绍抽屉容器Panel的用法。
效果图：
![img](P)  
这个Panel控件可以轻易实现不同方向的抽屉效果，比SlidingDrawer有更强的扩展性！
在多次使用Panel的过程中，发现Panel有个bug，会间断性出现“闪烁”，也就是在onTouchListener里面的触发ACTION_DOWN后，抽屉瞬间弹出然后瞬间回收。把原Panel的OnTouchListener，即以下代码：
```  
OnTouchListener touchListener = new OnTouchListener() {
	int initX;
	int initY;
	boolean setInitialPosition;
	public boolean onTouch(View v, MotionEvent event) {
		if (mState == State.ANIMATING) {
			// we are animating
			return false;
		}
		Log.d(TAG, "state: " + mState + " x: " + event.getX() + " y:
				+ event.getY());
		int action = event.getAction();
		if (action == MotionEvent.ACTION_DOWN) {
			if (mBringToFront) {
				bringToFront();
			}
			initX = 0;
			initY = 0;
			if (mContent.getVisibility() == GONE) {
				// since we may not know content dimensions we use factors
				// here
				if (mOrientation == VERTICAL) {
					initY = mPosition == TOP ? -1 : 1;
				} else {
					initX = mPosition == LEFT ? -1 : 1;
				}
			}
			setInitialPosition = true;
		} else {
			if (setInitialPosition) {
				// now we know content dimensions, so we multiply factors...
				initX *= mContentWidth;
				initY *= mContentHeight;
				// ... and set initial panel's position
				mGestureListener.setScroll(initX, initY);
				setInitialPosition = false;
				// for offsetLocation we have to invert values
				initX = -initX;
				initY = -initY;
			}
			// offset every ACTION_MOVE & ACTION_UP event
			event.offsetLocation(initX, initY);
		}
		if (!mGestureDetector.onTouchEvent(event)) {
			if (action == MotionEvent.ACTION_UP) {
				// tup up after scrolling
				post(startAnimation);
			}
		}
		return false;
	}
};
```
替换为：
```  
OnTouchListener touchListener = new OnTouchListener() {
	float touchX, touchY;
	public boolean onTouch(View v, MotionEvent event) {
		if (mState == State.ANIMATING) {
			return false;
		}
		int action = event.getAction();
		if (action == MotionEvent.ACTION_DOWN) {
			if (mBringToFront) {
				bringToFront();
			}
			touchX = event.getX();
			touchY = event.getY();
		}
		if (!mGestureDetector.onTouchEvent(event)) {
			if (action == MotionEvent.ACTION_UP) {
				int size = (int) (Math.abs(touchX - event.getX()) + Math
						.abs(touchY - event.getY()));
				if (size == mContentWidth || size == mContentHeight) {
					mState = State.ABOUT_TO_ANIMATE;
					// Log.e("size", String.valueOf(size));
					// Log.e(String.valueOf(mContentWidth),String.valueOf(mContentHeight));
				}
				post(startAnimation);
			}
		}
		return false;
	}
};
```