在Android游戏当中充当主要的除了控制类外就是显示类，在J2ME中我们用Display和Canvas来实现这些，而Google Android中涉及到显示的为view类，android游戏开发中比较重要和复杂的就是显示和游戏逻辑的处理。这里我们说下android.view.View和android.view.SurfaceView。SurfaceView是从View基类中派生出来的显示类，直接子类有GLSurfaceView和VideoView，可以看出GL和视频播放以及Camera摄像头一般均使用SurfaceView，到底有哪些优势呢? SurfaceView可以控制表面的格式，比如大小，显示在屏幕中的位置，最关键是的提供了SurfaceHolder类，使用getHolder方法获取，相关的有Canvas  lockCanvas() 
Canvas lockCanvas(Rect dirty)、void removeCallback(SurfaceHolder.Callback callback)、void unlockCanvasAndPost(Canvas canvas)控制图形以及绘制，而在SurfaceHolder.Callback接口回调中可以通过下面三个抽象类可以自己定义具体的实现，比如第一个更改格式和显示画面。
```  
abstract void  surfaceChanged(SurfaceHolder holder, int format, int width, int height)
abstract void  surfaceCreated(SurfaceHolder holder)
abstract void  surfaceDestroyed(SurfaceHolder holder)
```
对于Surface相关的，Android底层还提供了GPU加速功能，所以一般实时性很强的应用中主要使用SurfaceView而不是直接从View 构建，同时Android123未来后面说到的OpenGL中的GLSurfaceView也是从该类实现。