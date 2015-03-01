有关Android的自定义View的框架今天我们一起讨论下，对于常规的游戏，我们在View中需要处理以下几种问题: 1.控制事件 2.刷新View 3. 绘制View。
1. 对于控制事件今天我们只处理按键事件onKeyDown，以后的文章中将会讲到屏幕触控的具体处理onTouchEvent以及Sensor重力感应等方法。
2. 刷新view的方法这里主要有invalidate(int l, int t, int r, int b) 刷新局部，四个参数分别为左、上、右、下。整个view刷新 invalidate()，刷新一个矩形区域 invalidate(Rect dirty) ，刷新一个特性Drawable， invalidateDrawable(Drawable drawable) ，执行invalidate类的方法将会设置view为无效，最终导致onDraw方法被重新调用。由于今天的view比较简单，Android123提示大家如果在线程中刷新，除了使用handler方式外，可以在Thread中直接使用postInvalidate方法来实现。
3. 绘制View主要是onDraw()中通过形参canvas来处理，相关的绘制主要有drawRect、drawLine、drawPath等等。 view方法内部还重写了很多接口，其回调方法可以帮助我们判断出view的位置和大小，比如onMeasure(int, int) Called to determine the size requirements for this view and all of its children.  、onLayout(boolean, int, int, int, int) Called when this view should assign a size and position to all of its children 和onSizeChanged(int, int, int, int) Called when the size of this view has changed. 具体的作用，大家可以用Logcat获取当view变化时每个形参的变动。
下面cwjView是我们为今后游戏设计的一个简单自定义View框架，我们可以看到在Android平台自定义view还是很简单的，同时Java支持多继承可以帮助我们不断的完善复杂的问题。
```  
public class cwjView extends View {
	public cwjView(Context context) {
		super(context);
		setFocusable(true); // 允许获得焦点
		setFocusableInTouchMode(true); // 获取焦点时允许触控
	}
	@Override
	protected Parcelable onSaveInstanceState() { // 处理窗口保存事件
		Parcelable pSaved = super.onSaveInstanceState();
		Bundle bundle = new Bundle();
		// dosomething
		return bundle;
	}
	@Override
	protected void onRestoreInstanceState(Parcelable state) { // 处理窗口还原事件
		Bundle bundle = (Bundle) state;
		// dosomething
		super.onRestoreInstanceState(bundle.getParcelable("cwj"));
		return;
	}
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) // 处理窗口大小变化事件
	{
		super.onSizeChanged(w, h, oldw, oldh);
	}
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		super.onMeasure(widthMeasureSpec, heightMeasureSpec); // 如果不让父类处理记住调用setMeasuredDimension
	}
	@Override
	protected void onLayout(boolean changed, int left, int top, int right,
			int bottom) {
		super.onLayout(changed, left, top, ight, bottom);
	}
	@Override
	protected void onDraw(Canvas canvas) {
		Paint bg = new Paint();
		bg.setColor(Color.Red);
		canvas.drawRect(0, 0, getWidth() / 2, getHeight() / 2, bg); // 将view的左上角四分之一填充为红色

	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		return super.onTouchEvent(event); // 让父类处理屏幕触控事件
	}
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		// 处理按键事件，响应的轨迹球事件为
		// public boolean
		// onTrackballEvent
		// (MotionEvent
		// event)
		switch (keyCode) {
		case KeyEvent.KEYCODE_DPAD_UP:
			break;
		case KeyEvent.KEYCODE_DPAD_DOWN:
			break;
		case KeyEvent.KEYCODE_DPAD_LEFT:
			break;
		case KeyEvent.KEYCODE_DPAD_RIGHT:
			break;
		case KeyEvent.KEYCODE_DPAD_CENTER: // 处理中键按下
			break;
		default:
			return super.onKeyDown(keyCode, event);
		}
		return true;
	}
}
```
上面我们可以看到onMeasure使用的是父类的处理方法，如果我们需要解决自定义View的大小，可以尝试下面的方法
```  
@Override
protected void onMeasure (int widthMeasureSpec, int heightMeasureSpec)  
{
	height = View.MeasureSpec.getSize(heightMeasureSpec);
	width = View.MeasureSpec.getSize(widthMeasureSpec);
	setMeasuredDimension(width,height);  //这里面是原始的大小，需要重新计算可以修改本行
	//dosomething
}
```