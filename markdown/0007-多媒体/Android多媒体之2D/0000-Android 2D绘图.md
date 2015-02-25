我们这一部分主要展示如何从使用View类改为使用SurfaceView类。改变父类可以有助于你来绘制任何打算显示的东西而不必烦心于布局和XML文件。而且改变父类后你还可以更轻易地实现自定义的动画包括游戏。
首先将父类由View改为SurfaceView:
```  
class Panel extends SurfaceView {}
```
编译运行后，没有显示，只有黑屏。原因是onDraw()方法没有被调用。为了使得SurfaceView实时刷新，我们需要实现一个视图线程来处理绘制方法的调用。当然这也可以通过调用一个invalidate的方法来实现，但是如果使用一个线程的话，可以有更丰富的控制。因此让我们从SurfaceView最基本的部分开始，首先需要实现SurfaceHolder.Callback接口，该接口提供我们三种可以使用的方法。
```  
public class Panel extends SurfaceView implements SurfaceHolder.Callback {
	@Override
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}
	@Override
	public void surfaceCreated(SurfaceHolder holder) {
	}
	@Override
	public void surfaceDestroyed(SurfaceHolder holder) {
	}
}
```
为了注册该类作为回调接口，我们需要在构造函数中加入下面所示：
```  
public Panel(Context context) {
	super(context);
	mBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.icon);
	getHolder().addCallback(this);
}
```
下一步就是我们用来控制绘制Panel类的线程了：
```  
public class ViewThread extends Thread {
	private Panel mPanel;
	private SurfaceHolder mHolder;
	private boolean mRun = false;
	public ViewThread(Panel panel) {
		mPanel = panel;
		mHolder = mPanel.getHolder();
	}
	public void setRunning(boolean run) {
		mRun = run;
	}
	@Override
	public void run() {
		Canvas canvas = null;
		while (mRun) {
			canvas = mHolder.lockCanvas();
			if (canvas != null) {
				mPanel.doDraw(canvas);
				mHolder.unlockCanvasAndPost(canvas);
			}
		}
	}
}
```
这个线程是相当简单的。我们需要一个构造函数将Panel类作为参数。mHolder变量用来避免重复调用getHolder()。布尔值mRun是重要的，因为利用它来避免绘制时的死循环。
run()函数是关键，这里使用了while循环。当绘制时将canvas锁定。它的工作原理基本类似vertical sync，因此在绘制表面物件时不需要为该表面(surface)提供缓冲。
你或许注意到了，onDraw()函数名改为了doDraw()，这也很关键，因为现在只允许我们自己控制绘制。无论何时android系统打算使我们的panel无效，它会调用doDraw()函数，而该函数什么都不会做。
现在我们需要处理panel类中的线程了。来自这个SurfaceHolder.callback接口的方法是很不错的。当准备使用SurfaceView时，调用surfaceCreated()方法。这时我们的线程就启动了。这里我们还需要检查是否线程还是活动的，是否你使用了Home键返回了。
```  
private ViewThread mThread;
public Panel(Context context) {
	super(context);
	mBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.icon);
	getHolder().addCallback(this);
	mThread = new ViewThread(this);
}
@Override
public void surfaceCreated(SurfaceHolder holder) {
	if (!mThread.isAlive()) {
		mThread = new ViewThread(this);
		mThread.setRunning(true);
		mThread.start();
	}
}
@Override
public void surfaceDestroyed(SurfaceHolder holder) {
	if (mThread.isAlive()) {
		mThread.setRunning(false);
	}
}
```
首先我们需要一个控制该线程的线程变量。在构造函数中，我们实例化该线程。在surfaceCreated()中，我们检查是否该线程是活动的。如果它是活动的，就什么都不做，如果不是活动的，我们创建一个新的实例并且启动该线程。这里要注意我们不能复用可能存在的旧线程对象，因为在此时我们不确定该线程是否曾经运行过，而且线程实例也不能被启动超过一次。
当视图消亡时调用surfaceDestroyed()方法。可能的状况是activity进入后台，因为你打开了另一个程序，或者按下了返回键。这里我们要确保通过终结循环来使线程结束。这里只需要设置mRun变量为false就可以了。