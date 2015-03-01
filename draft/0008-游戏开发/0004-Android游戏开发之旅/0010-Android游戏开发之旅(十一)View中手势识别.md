有关Android平台的游戏开发中我们需要涉及到控制，在开始的android游戏开发之旅中我们提到了按键和轨迹球的控制方式，从今天开始给出大家游戏中其他的一些控制方式，比如今天的手势操作和未来重力感应。
很多网友发现Android中手势识别提供了两个类，由于Android 1.6以下的版本比如cupcake中无法使用android.view.GestureDetector，而 android.gesture.Gesture是Android1.6才开始支持的，我们考虑到仍然有很多Android1.5固件的网友，就来看下兼容性更强的android.view.GestureDetector。在android.view.GestureDetector类中有很多种重载版本，下面我们仅提到能够自定义在View中的两种方法，分别为 GestureDetector(Context context, GestureDetector.OnGestureListener listener) 和 GestureDetector(Context context, GestureDetector.OnGestureListener listener, Handler handler)。
我们可以看到第一个参数为Context，所以我们想附着到某View时，最简单的方法就是直接从超类派生传递Context，实现 GestureDetector里中提供一些接口。
下面我们就以实现手势识别的onFling动作，在CwjView中我们从View类继承，当然大家可以从TextView等更高层的界面中实现触控。
```  
class CwjView extends View {
	private GestureDetector mGD;
	public CwjView(Context context, AttributeSet attrs) {
		super(context, attrs);
		mGD = new GestureDetector(context,
				new GestureDetector.SimpleOnGestureListener() {
					public boolean onFling(MotionEvent e1, MotionEvent e2,
							float velocityX, float velocityY) {
						int dx = (int) (e2.getX() - e1.getX()); // 计算滑动的距离
						if (Math.abs(dx) > MAJOR_MOVE
								&& Math.abs(velocityX) > Math.abs(velocityY)) {
							// 降噪处理，必须有较大的动作才识别
							if (velocityX > 0) {
								// 向右边
							} else {
								// 向左边
							}
							return true;
						} else {
							return false; // 当然可以处理velocityY处理向上和向下的动作
						}
					}
				});
	}
}
```
在上面Android123提示大家仅仅探测了Fling动作仅仅实现了onFling方法，这里相关的还有以下几种方法来实现具体的可以参考我们以前的文章有详细的解释: 
```  
boolean onDoubleTap(MotionEvent e)
boolean onDoubleTapEvent(MotionEvent e) 
boolean onDown(MotionEvent e) 
void onLongPress(MotionEvent e) 
boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) 
void onShowPress(MotionEvent e) 
boolean onSingleTapConfirmed(MotionEvent e) 
boolean onSingleTapUp(MotionEvent e) 
```
接下来是重点，让我们的View接受触控，需要使用下面两个方法让GestureDetector类去处理onTouchEvent和 onInterceptTouchEvent方法。
```  
@Override
public boolean onTouchEvent(MotionEvent event) { 
	mGD.onTouchEvent(event);
	return true;
}
@Override
public boolean onInterceptTouchEvent(MotionEvent event) {
	return mGD.onTouchEvent(event);
}
```