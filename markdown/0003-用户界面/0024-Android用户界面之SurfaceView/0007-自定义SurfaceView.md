今天我们说下未来的Android游戏引擎模板架构问题，对于游戏我们还是选择SurfaceView，这里我们直接继承SurfaceView，实现SurfaceHolder.Callback接口，处理surfaceCreated、surfaceChanged以及surfaceDestroyed方法，这里我们并没有把按键控制传入，最终游戏的控制方面仍然由View内部类处理比较好，有关SurfaceView的具体我们可以参见Android开源项目的Camera中有关画面捕捉以及VideoView的控件实现大家可以清晰了解最终的用意。
```  
public class cwjView extends SurfaceView implements SurfaceHolder.Callback {
	public cwjView(Context context, AttributeSet attrs) {
		super(context, attrs);
		SurfaceHolder holder = getHolder();
		holder.addCallback(this);
		setFocusable(true);
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}
	public void surfaceCreated(SurfaceHolder holder) {
	}
	public void surfaceDestroyed(SurfaceHolder holder) {
	}
	@Override
	public void onWindowFocusChanged(boolean hasWindowFocus) {
	}
}
```