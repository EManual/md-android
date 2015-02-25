到这里，拖放开始处理的框框基本清楚，但是DragView的创建和显示还有必要进一步深究
```  
DragView dragView = mDragView = new DragView(mContext, b, registrationX, registrationY, textureLeft, textureTop, textureWidth, textureHeight);
```
函数参数说明：
```  
mContext = launcher
b = 根据拖放源view创建的大小一致的bitmap对象
registrationX = 手指点击位置与拖放源view 坐标x方向的偏移
registrationY = 手指点击位置与拖放源view 坐标y方向的偏移
textureLeft = 0;
textureTop = 0;
textureWidth = b.getWidth();
textureHeight = b.getHeight();
// 函数体
super(context);
// 获取window管理器
mWindowManager = WindowManagerImpl.getDefault();
// 一个动画，开始拖放时显示
mTween = new SymmetricalLinearTween(false, 110 /* ms duration */, this);
// 对源b 做一个缩放产生一个新的bitmap对象
Matrix scale = new Matrix();
float scaleFactor = width;
scaleFactor = mScale = (scaleFactor + DRAG_SCALE) / scaleFactor;
scale.setScale(scaleFactor, scaleFactor);
mBitmap = Bitmap.createBitmap(bitmap, left, top, width, height, scale,
		true);
// The point in our scaled bitmap that the touch events are located
mRegistrationX = registrationX + (DRAG_SCALE / 2);
mRegistrationY = registrationY + (DRAG_SCALE / 2);
```
其实函数很简单，就是记录一些参数，然后对view图片做一个缩放处理，并且准备一个tween动画，在长按桌面图标后图标跳跃到手指上显示该动画，了解这些，有助于理解函数dragView.show
```  
// windowToken来自与workspace.onattchtowindow时候获取的view
// 所有attch的window标识，有这个参数，可以把dragview添加到
// workspace所属的同一个window对象
// touchX,手指点击在屏幕的位置x
// touchy,手指点击在屏幕的位置y
public void show(IBinder windowToken, int touchX, int touchY) {
	WindowManager.LayoutParams lp;
	int pixelFormat;
	pixelFormat = PixelFormat.TRANSLUCENT;
	// 布局参数值的注意的是view位置参数，
	// x=touchX-mRegistrationX=touchX-(registrationX + (DRAG_SCALE /
	// 2))=手指点击位置-view坐标与手指点击位置偏差加上缩放值
	lp = new WindowManager.LayoutParams(
			ViewGroup.LayoutParams.WRAP_CONTENT,
			ViewGroup.LayoutParams.WRAP_CONTENT, touchX - mRegistrationX,
			touchY - mRegistrationY,
			WindowManager.LayoutParams.TYPE_APPLICATION_SUB_PANEL,
			WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN
					| WindowManager.LayoutParams.FLAG_LAYOUT_NO_LIMITS
			/* | WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM */,
			pixelFormat);
	// lp.token = mStatusBarView.getWindowToken();
	lp.gravity = Gravity.LEFT | Gravity.TOP;
	lp.token = windowToken;
	lp.setTitle("DragView");
	mLayoutParams = lp;
	// dragview的父类是Window,也就是说dragview可以拖放到屏幕的任意位置
	mWindowManager.addView(this, lp);
	mAnimationScale = 1.0f / mScale;
	// 播放开始拖动动画(直观感觉是图标变大了)
	mTween.start(true);
}
```