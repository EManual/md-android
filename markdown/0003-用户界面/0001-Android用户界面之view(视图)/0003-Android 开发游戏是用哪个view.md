在Android游戏当中充当主要的除了控制类外就是显示类，在J2ME中我们用Display和Canvas来实现这些，而Google Android中涉及到显示的为view类，android游戏开发中比较重要和复杂的就是显示和游戏逻辑的处理。 
这里我们说下android.view.View和android.view.SurfaceView。SurfaceView是从View基类中派生出来的显示类，直接子类有GLSurfaceView和VideoView，可以看出GL和视频播放以及Camera摄像头一般均使用SurfaceView，到底有哪些优势呢? 
SurfaceView可以控制表面的格式，比如大小，显示在屏幕中的位置，最关键是的提供了SurfaceHolder类，使用getHolder方法获取，相关的有Canvas lockCanvas()、Canvas lockCanvas(Rect dirty) 、void removeCallback(SurfaceHolder.Callback callback)、void unlockCanvasAndPost(Canvas canvas) 控制图形以及绘制，而在SurfaceHolder.Callback 接口回调中可以通过重写下面方法实现。 
使用的SurfaceView的时候，一般情况下要对其进行创建，销毁，改变时的情况进行监视，这就要用到 SurfaceHolder.Callback. 
```  
class View extends SurfaceView implements SurfaceHolder.Callback {
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}
	// 看其名知其义，在surface的大小发生改变时激发
	public void surfaceCreated(SurfaceHolder holder) {
	}
	// 同上，在创建时激发，一般在这里调用画图的线程。
	public void surfaceDestroyed(SurfaceHolder holder) {
	}
	// 同上，销毁时激发，一般在这里将画图的线程停止、释放。
} 
```
对于Surface相关的，Android底层还提供了GPU加速功能，所以一般实时性很强的应用中主要使用SurfaceView而不是直接从View构建，同时后来做android 3d OpenGL中的GLSurfaceView也是从该类实现。 
SurfaceView和View最本质的区别在于，surfaceView是在一个新起的单独线程中可以重新绘制画面而View必须在UI的主线程中更新画面。 
那么在UI的主线程中更新画面 可能会引发问题，比如你更新画面的时间过长，那么你的主UI线程会被你正在画的函数阻塞。那么将无法响应按键，触屏等消息。 
当使用surfaceView由于是在新的线程中更新画面所以不会阻塞你的UI主线程。但这也带来了另外一个问题，就是事件同步。比如你触屏了一下，你需要surfaceView中thread处理，一般就需要有一个event queue的设计来保存touch event，这会稍稍复杂一点，因为涉及到线程同步。 
所以基于以上，根据游戏特点，一般分成两类。 
1、被动更新画面的。比如棋类，这种用view就好了。因为画面的更新是依赖于 onTouch 来更新，可以直接使用invalidate。 因为这种情况下，这一次Touch和下一次的Touch需要的时间比较长些，不会产生影响。 
2、主动更新。比如一个人在一直跑动。这就需要一个单独的thread不停的重绘人的状态，避免阻塞main UI thread。所以显然view不合适，需要surfaceView来控制。
3、Android中的SurfaceView类就是双缓冲机制。因此，开发游戏时尽量使用SurfaceView而不要使用View，这样的话效率较高，而且SurfaceView的功能也更加完善。