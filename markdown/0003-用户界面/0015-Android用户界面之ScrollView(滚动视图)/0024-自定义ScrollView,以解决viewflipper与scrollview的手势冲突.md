自定义ScrollView，并重写其onTouchEvent和dispatchTouchEvent方法，以解决viewflipper 与scrollview的手势冲突，不多说直接来代码：
```  
import android.content.Context;
import android.util.AttributeSet;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.widget.ScrollView;
 /**
  * 自定义ScrollView，并重写其onTouchEvent和dispatchTouchEvent方法， 以解决viewflipper
  * 与scrollview的手势冲突
  */
public class MyScrollView extends ScrollView {
	GestureDetector gestureDetector;
	public MyScrollView(Context context) {
		super(context);
	}
	public MyScrollView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public MyScrollView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	public void setGestureDetector(GestureDetector gestureDetector) {
		this.gestureDetector = gestureDetector;
	}
	@Override
	public boolean onTouchEvent(MotionEvent ev) {
		super.onTouchEvent(ev);
		return gestureDetector.onTouchEvent(ev);
	}
	@Override
	public boolean dispatchTouchEvent(MotionEvent ev) {
		gestureDetector.onTouchEvent(ev);
		super.dispatchTouchEvent(ev);
		return true;
	}
}
```