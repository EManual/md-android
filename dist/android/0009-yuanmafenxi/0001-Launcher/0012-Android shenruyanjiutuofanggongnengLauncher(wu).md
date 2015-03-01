拖放过程总的处理思路就是根据当前坐标位置获取dropTarget的目标位置，然后又根据相关状态和坐标位置调用dropTarget的对应生命周期函数，这里面有两个点需要进一步深入了解，一是查找dropTarget：findDropTarget(screenX, screenY, coordinates)，二是mHandler.postDelayed(mScrollRunnable, SCROLL_DELAY);
#### 1.findDropTarget
```  
private DropTarget findDropTarget(int x, int y, int[] dropCoordinates) {
	final Rect r = mRectTemp;
	// mDropTargets是一个拖放目标view别表，在laucher初始化等被添加
	final ArrayList dropTargets = mDropTargets;
	final int count = dropTargets.size();
	// 遍历dropTargets列表，查看{x,y}是否落在dropTarget坐标区域，若是，返回dropTarget。
	for (int i = count - 1; i >= 0; i--) {
		final DropTarget target = dropTargets.get(i);
		target.getHitRect(r);
		// 获取target左上角屏幕坐标
		target.getLocationOnScreen(dropCoordinates);
		r.offset(dropCoordinates[0] - target.getLeft(), dropCoordinates[1]
				- target.getTop());
		if (r.contains(x, y)) {
			dropCoordinates[0] = x - dropCoordinates[0];
			dropCoordinates[1] = y - dropCoordinates[1];
			return target;
		}
	}
	return null;
}
```
#### 2.mScrollRunnable
看mScrollRunnable对象的构造类，通过setDirection设置滚动方向，然后通过一步调用DragScroller.scrollLeft/scrollRight来对桌面进行向左向右滚动。
```  
private class ScrollRunnable implements Runnable {
	private int mDirection;
	ScrollRunnable() {
	}
	public void run() {
		if (mDragScroller != null) {
			if (mDirection == SCROLL_LEFT) {
				mDragScroller.scrollLeft();
			} else {
				mDragScroller.scrollRight();
			}
			mScrollState = SCROLL_OUTSIDE_ZONE;
		}
	}
	void setDirection(int direction) {
		mDirection = direction;
	}
}
```
#### 3.拖放结束，入口还是在DragController.onTouchEvent(MotionEvent ev)
先看调用堆栈：
```  
at com.android.launcher2.DragController.endDrag
at com.android.launcher2.DragController.onTouchEvent
at com.android.launcher2.DragLayer.onTouchEvent
at android.view.View.dispatchTouchEvent
```
onTouchEvent关键代码：
```  
case MotionEvent.ACTION_UP:
mHandler.removeCallbacks(mScrollRunnable);
if (mDragging) {
	// 拖动过程手指离开屏幕
	drop(screenX, screenY);
}
endDrag();
break;
```