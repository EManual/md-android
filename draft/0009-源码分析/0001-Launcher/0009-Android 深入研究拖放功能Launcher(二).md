看了上面代码知道，当开始点击桌面时，celllayout就会根据点击区域去查找在该区域是否有child存在，若有把它设置为tag.cell,没有,tag.cell设置为null,后面在开始拖放时launcher.onlongclick中对tag进行处理，这个理顺了，再深入到workspace.startDrag函数，workspace.startDrag调用DragController.startDrag去处理拖放。
```  
mDragController.startDrag(child, this, child.getTag(), DragController.DRAG_ACTION_MOVE);
```
再分析一下上面调用的几个参数
```  
child = tag.cell
this = workspace
```
child.getTag()是什么呢?在什么时候被设置?再仔细回顾原来launcher加载过程代码，在launcher.createShortcut中它被设置了：注意下面我代码中的注释
```  
View createShortcut(int layoutResId, ViewGroup parent, ShortcutInfo info) {
	TextView favorite = (TextView) mInflater.inflate(layoutResId, parent,
			false);
	favorite.setCompoundDrawablesWithIntrinsicBounds(null,
			new FastBitmapDrawable(info.getIcon(mIconCache)), null, null);
	favorite.setText(info.title);
	// 设置favorite(一个桌面Shortcut类型的图标)的tag
	favorite.setTag(info);
	favorite.setOnClickListener(this);
	return favorite;
}
```
继续深入解读DragController.startDrag函数：
```  
public void startDrag(View v, DragSource source, Object dragInfo,
		int dragAction) {
	// 设置拖放源view
	mOriginator = v;
	// 获取view的bitmap
	Bitmap b = getViewBitmap(v);
	if (b == null) {
		// out of memory?
		return;
	}
	// 获取源view在整个屏幕的坐标
	int[] loc = mCoordinatesTemp;
	v.getLocationOnScreen(loc);
	int screenX = loc[0];
	int screenY = loc[1];
	// 该函数功能解读请继续往下看
	startDrag(b, screenX, screenY, 0, 0, b.getWidth(), b.getHeight(),
			source, dragInfo, dragAction);
	b.recycle();
	// 设置原来view不可见
	if (dragAction == DRAG_ACTION_MOVE) {
		v.setVisibility(View.GONE);
	}
}
public void startDrag(Bitmap b, int screenX, int screenY, int textureLeft,
		int textureTop, int textureWidth, int textureHeight,
		DragSource source, Object dragInfo, int dragAction) {
	// 隐藏软键盘
	if (mInputMethodManager == null) {
		mInputMethodManager = (InputMethodManager) mContext
				.getSystemService(Context.INPUT_METHOD_SERVICE);
	}
	mInputMethodManager.hideSoftInputFromWindow(mWindowToken, 0);
	// mListener = deletezone,在blog laucher ui框架中有说明该函数，主要就是现实deletezone
	if (mListener != null) {
		mListener.onDragStart(source, dragInfo, dragAction);
	}
	// 记住手指点击位置与屏幕左上角位置偏差
	int registrationX = ((int) mMotionDownX) - screenX;
	int registrationY = ((int) mMotionDownY) - screenY;
	mTouchOffsetX = mMotionDownX - screenX;
	mTouchOffsetY = mMotionDownY - screenY;
	mDragging = true;
	mDragSource = source;
	mDragInfo = dragInfo;
	mVibrator.vibrate(VIBRATE_DURATION);
	// 创建DragView对象
	DragView dragView = mDragView = new DragView(mContext, b,
			registrationX, registrationY, textureLeft, textureTop,
			textureWidth, textureHeight);
	// 显示Dragview对象
	dragView.show(mWindowToken, (int) mMotionDownX, (int) mMotionDownY);
}
```