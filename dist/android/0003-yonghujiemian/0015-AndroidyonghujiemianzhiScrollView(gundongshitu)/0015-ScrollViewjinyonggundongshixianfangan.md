看到有人提到如何让scrollview禁止scroll功能，碰巧昨天自己用到，于是解决了这个问题，下面将主要实现代码附上，希望对大家有所帮助~实现原理其实很简单，默认onTouchEvent方法，返回值为true，只要重载父类的该方法，将返回值改为false，即可实现。
```  
public class MyHorizontalScrollView extends HorizontalScrollView {
	public MyHorizontalScrollView(Context context) {
		super(context);
	}
	public MyHorizontalScrollView(Context context, AttributeSet attrs,
			int defStyle) {
		super(context, attrs, defStyle);
	}
	public MyHorizontalScrollView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	@Override
	public boolean arrowScroll(int direction) {
		Log.e("boolean", super.arrowScroll(direction) + "");
		return super.arrowScroll(direction);
	}
	@Override
	public boolean onTouchEvent(MotionEvent ev) {
		Log.d("touchevent", "touchevent" + super.onTouchEvent(ev));
		// return super.onTouchEvent(ev);
		return false;
	}
}
```