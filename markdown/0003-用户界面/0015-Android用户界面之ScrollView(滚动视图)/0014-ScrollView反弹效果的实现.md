View中也有scrollBy和scrollTo这两个方法，但是ScrollView对scrollTo进行重写  
由于：
```  
public void scrollBy(int x, int y) {
	scrollTo(mScrollX + x, mScrollY + y);
}
```
View:
```  
public void scrollTo(int x, int y) {
	if (mScrollX != x || mScrollY != y) {
		int oldX = mScrollX;
		int oldY = mScrollY;
		mScrollX = x;
		mScrollY = y;
		onScrollChanged(mScrollX, mScrollY, oldX, oldY);
		if (!awakenScrollBars()) {
			invalidate();
		}
	}
}
```
所以也就相当于scrollBy和scrollTo这两个方法都被重写了。重写的代码中加入校验，当你移动到最上面或者最下面的时候无法再向上移动或向下移动。这就导致了如果简单调用scrollTo无法实现继续移动。如果你还要继续移动的话mScrollY就为0或者是你内部视图的测量高度-ScrollView的高度。scrollTo是移动到，scrollBy是移动了。如下图：
![img](http://emanual.github.io/md-android/img/view_srollview/15_srollview.jpg)   
所以内容向下移动，手指向上滑动，那这个deltaY就为正，也就是mScrollY=mScrollY+deltaY，mScrollY变大直到移动到最下无法移动位置；反之mScrollY 变小直到为0移动到最上面为止。
```  
final float preY = y;
float nowY = ev.getY();
int deltaY = (int) (preY - nowY);
// 滚动,deltaY 为移动的距离，如果要用scrollTo需要计算准确的位置，也就是先前的位置在加上现在移动了多少
scrollBy(0, deltaY);
```
基于这些就可以重写ScrollView的onTouchEvent并结合ScrollView的内部视图的layout()方法、TranslateAnimation()实现反弹效果。
ScrollView代码：
```  
 /**
  * ScrollView反弹效果的实现
  */
public class MyScrollView extends ScrollView {
	private View inner;
	private float y;
	private Rect normal = new Rect();;
	public MyScrollView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	@Override
	protected void onFinishInflate() {
		if (getChildCount() > 0) {
			inner = getChildAt(0);
		}
	}
	@Override
	public boolean onTouchEvent(MotionEvent ev) {
		if (inner == null) {
			return super.onTouchEvent(ev);
		} else {
			commOnTouchEvent(ev);
		}
		return super.onTouchEvent(ev);
	}
	public void commOnTouchEvent(MotionEvent ev) {
		int action = ev.getAction();
		switch (action) {
		case MotionEvent.ACTION_DOWN:
			y = ev.getY();
			break;
		case MotionEvent.ACTION_UP:
			if (isNeedAnimation()) {
				animation();
			}
			break;
		case MotionEvent.ACTION_MOVE:
			final float preY = y;
			float nowY = ev.getY();
			int deltaY = (int) (preY - nowY);
			// 滚动
			scrollBy(0, deltaY);
			y = nowY;
			// 当滚动到最上或者最下时就不会再滚动，这时移动布局
			if (isNeedMove()) {
				if (normal.isEmpty()) {
					// 保存正常的布局位置
					normal.set(inner.getLeft(), inner.getTop(),
							inner.getRight(), inner.getBottom());
				}
				// 移动布局
				inner.layout(inner.getLeft(), inner.getTop() - deltaY,
						inner.getRight(), inner.getBottom() - deltaY);
			}
			break;
		default:
			break;
		}
	}
	// 开启动画移动
	public void animation() {
		// 开启移动动画
		TranslateAnimation ta = new TranslateAnimation(0, 0, inner.getTop(), normal.top);
		ta.setDuration(200);
		inner.startAnimation(ta);
		// 设置回到正常的布局位置
		inner.layout(normal.left, normal.top, normal.right, normal.bottom);
		normal.setEmpty();
	}
	// 是否需要开启动画
	public boolean isNeedAnimation() {
		return !normal.isEmpty();
	}
	// 是否需要移动布局
	public boolean isNeedMove() {
		int offset = inner.getMeasuredHeight() - getHeight();
		int scrollY = getScrollY();
		if (scrollY == 0 || scrollY == offset) {
			return true;
		}
		return false;
	}
}
```