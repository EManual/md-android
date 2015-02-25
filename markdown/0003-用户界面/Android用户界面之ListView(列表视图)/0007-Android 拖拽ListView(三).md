看到上面的一大堆变量和操作，你可能有些眼花缭乱，在后面使用的时候回头再去理解也可。开始拖动影像startDrag()方法：
```  
[Tags]/**
 [Tags]* 准备拖动，初始化拖动项的图像
 [Tags]* 
 [Tags]* @param bm
 [Tags]* @param y
 [Tags]*/
public void startDrag(Bitmap bm ,int y){
	//释放影像，在准备影像的时候，防止影像没释放，每次都执行一下
	stopDrag();
	windowParams = new WindowManager.LayoutParams();
	//从上到下计算y方向上的相对位置，
	windowParams.gravity = Gravity.TOP;
	windowParams.x = 0;
	windowParams.y = y - dragPoint + dragOffset;
	windowParams.width = WindowManager.LayoutParams.WRAP_CONTENT;
	windowParams.height = WindowManager.LayoutParams.WRAP_CONTENT;
	//下面这些参数能够帮助准确定位到选中项点击位置，照抄即可
	windowParams.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE
	WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE
	WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON
	WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN;
	windowParams.format = PixelFormat.TRANSLUCENT;
	windowParams.windowAnimations = 0;

	//把影像ImagView添加到当前视图中
	ImageView imageView = new ImageView(getContext());
	imageView.setImageBitmap(bm);
	windowManager = (WindowManager)getContext().getSystemService("window");
	windowManager.addView(imageView, windowParams);
	//把影像ImageView引用到变量drawImageView，用于后续操作(拖动，释放等等)
	dragImageView = imageView;
}
```
stopDrag()方法如下：
```  
[Tags]/**
	 [Tags]* 停止拖动，去除拖动项的头像
	 [Tags]*/
	public void stopDrag() {
		if (dragImageView != null) {
			windowManager.removeView(dragImageView);
			dragImageView = null;
		}
	}
```
7.重写onTouchEvent()方法。
在这个方法中我们主要是处理拖动和放下。
拖动是选中项的影像随着手指滑动；放下是在拖动结束的时候交换数据。
方法的整体结构如下：
```  
[Tags]/**
 [Tags]* 触摸事件
 [Tags]*/
@Override
public boolean onTouchEvent(MotionEvent ev) {
	// 如果dragmageView为空，说明拦截事件中已经判定仅仅是点击，不是拖动，返回
	// 如果点击的是无效位置，返回，需要重新判断
	if (dragImageView != null && dragPosition != INVALID_POSITION) {
		int action = ev.getAction();
		switch (action) {
		case MotionEvent.ACTION_UP:
			int upY = (int) ev.getY();
			// 释放拖动影像
			stopDrag();
			// 放下后，判断位置，实现相应的位置删除和插入
			onDrop(upY);
			break;
		case MotionEvent.ACTION_MOVE:
			int moveY = (int) ev.getY();
			// 拖动影像
			onDrag(moveY);
			break;
		default:
			break;
		}
		return true;
	}
	// 这个返回值能够实现selected的选中效果，如果返回true则无选中效果
	return super.onTouchEvent(ev);
}
```