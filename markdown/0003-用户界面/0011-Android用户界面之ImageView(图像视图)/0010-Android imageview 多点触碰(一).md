一种是利用Matrix的postTranslate和postScale方法分别进行移动和缩放。
这种方式实质是对ImageView中的drawable进行缩放和移动。
imageview组件本身并没有移动和缩放，这种方法实现起来比较简单，但是不知道如何获得经过移动后的drawable的坐标和大小比较郁闷，因为调用imageview的各种方法拿到的都是其本身的大小和坐标。
而另一种是直接对imageview进行操作，直接移动和改变组件本身的大小从而实现移动和缩放。
核心类 继承->ImageView 并加入了一些动画效果。
```  
import Android.content.Context;
import Android.util.FloatMath;
import Android.view.MotionEvent;
import Android.view.animation.TranslateAnimation;
import Android.widget.ImageView;
 /**
  * 继承ImageView 实现了多点触碰的拖动和缩放
  */
public class TouchView extends ImageView {
	static final int NONE = 0;
	static final int DRAG = 1;
	// 拖动中
	static final int ZOOM = 2;
	// 缩放中
	static final int BIGGER = 3;
	// 放大ing
	static final int SMALLER = 4;
	// 缩小ing
	private int mode = NONE;
	// 当前的事件
	private float beforeLenght;
	// 两触点距离
	private float afterLenght;
	// 两触点距离
	private float scale = 0.04f;
	// 缩放的比例 X Y方向都是这个值 越大缩放的越快
	private int screenW;
	private int screenH;
	/* 处理拖动 变量 */
	private int start_x;
	private int start_y;
	private int stop_x;
	private int stop_y;
	private TranslateAnimation trans;

	// 处理超出边界的动画
	public TouchView(Context context, int w, int h) {
		super(context);
		this.setPadding(0, 0, 0, 0);
		screenW = w;
		screenH = h;
	}

	 /**
	  * 就算两点间的距离
	  */
	private float spacing(MotionEvent event) {
		float x = event.getX(0) - event.getX(1);
		float y = event.getY(0) - event.getY(1);
		return FloatMath.sqrt(x * x + y * y);
	}

	 /**
	  * 处理触碰..
	  */
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		switch (event.getAction() & MotionEvent.ACTION_MASK) {
		case MotionEvent.ACTION_DOWN:
			mode = DRAG;
			stop_x = (int) event.getRawX();
			stop_y = (int) event.getRawY();
			start_x = (int) event.getX();
			start_y = stop_y - this.getTop();
			if (event.getPointerCount() == 2)
				beforeLenght = spacing(event);
			break;
		case MotionEvent.ACTION_POINTER_DOWN:
			if (spacing(event) > 10f) {
				mode = ZOOM;
				beforeLenght = spacing(event);
			}
			break;
		case MotionEvent.ACTION_UP:
			/* 判断是否超出范围 并处理 */
			int disX = 0;
			int disY = 0;
			if (getHeight() <= screenH || this.getTop() < 0) {
				if (this.getTop() < 0) {
					int dis = getTop();
					this.layout(this.getLeft(), 0, this.getRight(),
							0 + this.getHeight());
					disY = dis - getTop();
				} else if (this.getBottom() > screenH) {
					disY = getHeight() - screenH + getTop();
					this.layout(this.getLeft(), screenH - getHeight(),
							this.getRight(), screenH);
				}
			}
			if (getWidth() <= screenW) {
				if (this.getLeft() < 0) {
					disX = getLeft();
					this.layout(0, this.getTop(), 0 + getWidth(),
							this.getBottom());
				} else if (this.getRight() > screenW) {
					disX = getWidth() - screenW + getLeft();
					this.layout(screenW - getWidth(), this.getTop(), screenW,
							this.getBottom());
				}
			}
		}
	}
}
```