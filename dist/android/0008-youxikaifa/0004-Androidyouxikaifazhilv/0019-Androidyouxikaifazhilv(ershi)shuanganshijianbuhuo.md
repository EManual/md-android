对于游戏开发中我们可能经常要用到双按屏幕，在Android 1.6以前并没有提供完善的手势识别类，在Android 1.5 SDK中我们可以找到android.view.GestureDetector.OnDoubleTapListener，但是经过测试仍然无法正常工作，最终我们使用的解决方法如下：
测试代码: 
```  
public class TouchLayout extends RelativeLayout {
	public Handler doubleTapHandler = null;
	protected long lastDown = -1;
	public final static long DOUBLE_TIME = 500;
	public TouchLayout(Context context) {
		super(context);
	}
	public TouchLayout(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public TouchLayout(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	public boolean onTouchEvent(MotionEvent event) {
		this.handleEvent(event);
		if (event.getAction() == MotionEvent.ACTION_DOWN) {
			long nowDown = System.currentTimeMillis();
			if (nowDown - lastDown < DOUBLE_TIME) {
				if (doubleTapHandler != null)
					doubleTapHandler.sendEmptyMessage(-1);
			} else {
				lastDown = nowDown;
			}
		}
		return true;
	}
	protected void handleEvent(MotionEvent event) {
		switch (event.getAction()) {
		case MotionEvent.ACTION_DOWN:
			// Do sth 这里处理即可
			break;
		case MotionEvent.ACTION_UP:
			// Do sth
			break;
		}
	}
}
```