Luancher有一个相对比较复杂的功能就是拖放功能，要深入了解launcher，深入理解拖放功能是有必要的，我将对launcher的拖放功能做深入的了解。
1.首先直观感受什么时候开始拖放?我们长按桌面一个应用图标或者控件的时候拖放就开始了，包括在all app view中长按应用图标，下面就是我截取的拖放开始的代码调用堆栈：
```  	
at com.android.launcher2.DragController.startDrag
at com.android.launcher2.Workspace.startDrag
at com.android.launcher2.Launcher.onLongClick
at android.view.View.performLongClick
at android.widget.TextView.performLongClick
at android.view.View$CheckForLongPress.run
at android.os.Handler.handleCallback
at android.os.Handler.dispatchMessage
at android.os.Looper.loop
```
桌面应用图标由Launcher.onLongClick负责监听处理，插入断点debug进入onLongclick函数：
```  
if (!(v instanceof CellLayout)) {
	v = (View) v.getParent();
}
// 获取桌面CellLayout上一个被拖动的对象
CellLayout.CellInfo cellInfo = (CellLayout.CellInfo) v.getTag();
if (mWorkspace.allowLongPress()) {
	if (cellInfo.cell == null) {
	} else {
		if (!(cellInfo.cell instanceof Folder)) {
			// 调用Workspace.startDrag处理拖动
			mWorkspace.startDrag(cellInfo);
		}
	}
}
```
我上面只写出关键代码，首先是获取被拖动的对象v.getTag()，Tag什么时候被设置进去的了
```  
public boolean onInterceptTouchEvent(MotionEvent ev) {
	if (action == MotionEvent.ACTION_DOWN) {
		boolean found = false;
		for (int i = count - 1; i >= 0; i--) {
			final View child = getChildAt(i);
			if ((child.getVisibility()) == VISIBLE
					|| child.getAnimation() != null) {
				child.getHitRect(frame);
				// 判断区域是否在这个子控件的区间,如果有把child信息赋给mCellInfo
				if (frame.contains(x, y)) {
					final LayoutParams lp = (LayoutParams) child
							.getLayoutParams();
					cellInfo.cell = child;
					cellInfo.cellX = lp.cellX;
					cellInfo.cellY = lp.cellY;
					cellInfo.spanX = lp.cellHSpan;
					cellInfo.spanY = lp.cellVSpan;
					cellInfo.valid = true;
					found = true;
					mDirtyTag = false;
					break;
				}
			}
		}
		mLastDownOnOccupiedCell = found;
		if (!found) {
			// 没有child view 说明没有点击桌面图标项
			cellInfo.cell = null;
		}
		setTag(cellInfo);
	}
}
```