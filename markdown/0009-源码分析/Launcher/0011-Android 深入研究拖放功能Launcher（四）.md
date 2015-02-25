#### 2，拖放过程
拖放过程的处理需要深入了解DragController.onTouchEvent(MotionEvent ev)函数的实现，我下面列出关键的MotionEvent.ACTION_MOVE部分代码并作出注释说明
```  
case MotionEvent.ACTION_MOVE:
// 根据手指坐标移动dragview
mDragView.move((int) ev.getRawX(), (int) ev.getRawY());
// 根据手指所在屏幕坐标获取目前所在的拖放目的view
final int[] coordinates = mCoordinatesTemp;
DropTarget dropTarget = findDropTarget(screenX, screenY, coordinates);
// 根据不同状态调用DropTarget的生命周期处理函数
if (dropTarget != null) {
	if (mLastDropTarget == dropTarget) {
		dropTarget.onDragOver(mDragSource, coordinates[0],
				coordinates[1], (int) mTouchOffsetX,
				(int) mTouchOffsetY, mDragView, mDragInfo);
	} else {
		if (mLastDropTarget != null) {
			mLastDropTarget.onDragExit(mDragSource, coordinates[0],
					coordinates[1], (int) mTouchOffsetX,
					(int) mTouchOffsetY, mDragView, mDragInfo);
		}
		dropTarget.onDragEnter(mDragSource, coordinates[0],
				coordinates[1], (int) mTouchOffsetX,
				(int) mTouchOffsetY, mDragView, mDragInfo);
	}
} else {
	if (mLastDropTarget != null) {
		mLastDropTarget.onDragExit(mDragSource, coordinates[0],
				coordinates[1], (int) mTouchOffsetX,
				(int) mTouchOffsetY, mDragView, mDragInfo);
	}
}
mLastDropTarget = dropTarget;
// 判断是否在delete区域
boolean inDeleteRegion = false;
if (mDeleteRegion != null) {
	inDeleteRegion = mDeleteRegion.contains(screenX, screenY);
}
// 不在delete区域，在左边切换区
if (!inDeleteRegion && screenX < SCROLL_ZONE) {
	if (mScrollState == SCROLL_OUTSIDE_ZONE) {
		mScrollState = SCROLL_WAITING_IN_ZONE;
		mScrollRunnable.setDirection(SCROLL_LEFT);
		mHandler.postDelayed(mScrollRunnable, SCROLL_DELAY);
	}
}
// 不在delete区，在右边切换区
else if (!inDeleteRegion
		&& screenX > scrollView.getWidth() - SCROLL_ZONE) {
	if (mScrollState == SCROLL_OUTSIDE_ZONE) {
		mScrollState = SCROLL_WAITING_IN_ZONE;
		mScrollRunnable.setDirection(SCROLL_RIGHT);
		mHandler.postDelayed(mScrollRunnable, SCROLL_DELAY);
	}
}
// 在delete区域
else {
	if (mScrollState == SCROLL_WAITING_IN_ZONE) {
		mScrollState = SCROLL_OUTSIDE_ZONE;
		mScrollRunnable.setDirection(SCROLL_RIGHT);
		mHandler.removeCallbacks(mScrollRunnable);
	}
}
break;
}
```