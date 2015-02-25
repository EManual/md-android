今天Android123开始新的android游戏开发之旅系列，主要从控制方法(按键、轨迹球、触屏、重力感应、摄像头、话筒气流、光线亮度)、图形 View(高效绘图技术如双缓冲)、音效(游戏音乐)以及最后的OpenGL ES(Java层)和NDK的OpenGL和J2ME游戏移植到Android方法，当然还有一些游戏实现惯用方法，比如地图编辑器，在Android OpenGL如何使用MD2文件，个部分讲述下Android游戏开发的过程最终实现一个比较完整的游戏引擎。相信大家都清楚Android Market下载量比较好的都是游戏，未来手机网游的发展相信Android使用的Java在这方面有比iPhone有更低的入门门槛。
对于很多游戏使用屏幕控制一般需要考虑长按事件，比如在动作类的游戏中需要长按发射武器，结合Android Button模型，我们实现一个带图片的Button的长按，为了更清晰的显示原理，Android开发网这里使用ImageButton作为基类
```  
public class RepeatingImageButton extends ImageButton {
	private long mStartTime; // 记录长按开始
	private int mRepeatCount; // 重复次数计数
	private RepeatListener mListener;
	private long mInterval = 500; // Timer触发间隔，即每0.5秒算一次按下
	public RepeatingImageButton(Context context) {
		this(context, null);
	}
	public RepeatingImageButton(Context context, AttributeSet attrs) {
		this(context, attrs, android.R.attr.imageButtonStyle);
	}
	public RepeatingImageButton(Context context, AttributeSet attrs,
			int defStyle) {
		super(context, attrs, defStyle);
		setFocusable(true); // 允许获得焦点
		setLongClickable(true); // 启用长按事件
	}
	public void setRepeatListener(RepeatListener l, long interval) { // 实现重复按下事件listener
		mListener = l;
		mInterval = interval;
	}
	@Override
	public boolean performLongClick() {
		mStartTime = SystemClock.elapsedRealtime();
		mRepeatCount = 0;
		post(mRepeater);
		return true;
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_UP) { // 本方法原理同onKeyUp的一样，这里处理屏幕事件，下面的onKeyUp处理Android手机上的物理按键事件
			removeCallbacks(mRepeater);
			if (mStartTime != 0) {
				doRepeat(true);
				mStartTime = 0;
			}
		}
		return super.onTouchEvent(event);
	}
	// 处理导航键事件的中键或轨迹球按下事件
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		switch (keyCode) {
		case KeyEvent.KEYCODE_DPAD_CENTER:
		case KeyEvent.KEYCODE_ENTER:
			super.onKeyDown(keyCode, event);
			return true;
		}
		return super.onKeyDown(keyCode, event);
	}
	// 当按键弹起通知长按结束
	@Override
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		switch (keyCode) {
		case KeyEvent.KEYCODE_DPAD_CENTER:
		case KeyEvent.KEYCODE_ENTER:

			removeCallbacks(mRepeater); // 取消重复listener捕获
			if (mStartTime != 0) {
				doRepeat(true); // 如果长按事件累计时间不为0则说明长按了
				mStartTime = 0; // 重置长按计时器
			}
		}
		return super.onKeyUp(keyCode, event);
	}
	private Runnable mRepeater = new Runnable() { // 在线程中判断重复
		public void run() {
			doRepeat(false);
			if (isPressed()) {
				postDelayed(this, mInterval); // 计算长按后延迟下一次累加
			}
		}
	};
	private void doRepeat(boolean last) {
		long now = SystemClock.elapsedRealtime();
		if (mListener != null) {
			mListener.onRepeat(this, now - mStartTime, last ? -1
					: mRepeatCount++);
		}
	}
}
```
下面是重复Button Listener接口的定义，调用时在Button中先使用setRepeatListener()方法实现RepeatListener接口
```  
public interface RepeatListener {
	void onRepeat(View v, long duration, int repeatcount); // 参数一为用户传入的Button对象，参数二为延迟的毫秒数，第三位重复次数回调。
}
```
本类大家可以直接在自己的View中implements实现RepeatListener接口即可。