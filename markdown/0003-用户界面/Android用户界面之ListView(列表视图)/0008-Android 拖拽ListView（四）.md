8.拖动影像。
拖动的时候，我们调用了onDrag(int y)方法，主要做的事情是，让选中项的影像随这手指滑动起来。如下：
```  
if (dragImageView != null) {
	// 设置一点点的透明度
	windowParams.alpha = 0.8f;
	// 更新y坐标位置
	windowParams.y = y - dragPoint + dragOffset;
	// 更新界面
	windowManager.updateViewLayout(dragImageView, windowParams);
}
```
当数据集合很大的时候，还需要在拖动到上部区域或者下部区域的时候滚动列表，使用ListView自带的方法setSelectionFromTop()。
一个可以滚动的拖拽列表雏形就出来了，最终onDrag()方法代码如下:
```  
[Tags]/**
 [Tags]* 拖动执行，在Move方法中执行
 [Tags]* @param y
 [Tags]*/
public void onDrag(int y) {
	if (dragImageView != null) {
		windowParams.alpha = 0.8f;
		windowParams.y = y - dragPoint + dragOffset;
		windowManager.updateViewLayout(dragImageView, windowParams);
	}
	// 为了避免滑动到分割线的时候，返回-1的问题
	int tempPosition = pointToPosition(0, y);
	if (tempPosition != INVALID_POSITION) {
		dragPosition = tempPosition;
	}
	// 滚动
	int scrollHeight = 0;
	if (y < upScrollBounce) {
		scrollHeight = 8;// 定义向上滚动8个像素，如果可以向上滚动的话
	} else if (y > downScrollBounce) {
		scrollHeight = -8;// 定义向下滚动8个像素，，如果可以向上滚动的话
	}
	if (scrollHeight != 0) {
		// 真正滚动的方法setSelectionFromTop()
		setSelectionFromTop(dragPosition,
				getChildAt(dragPosition - getFirstVisiblePosition())
						.getTop() + scrollHeight);
	}
}
```
9.放下影像，数据更新。
上面实现了拖动的效果，放下影像后：
1）我们要获取放下的位置是数据集合的哪一项；
2）在放下位置项插入拖动数据，并删除拖动数据原位置项
这些处理写在了onDrop()方法中,在ACTION_UP动作中执行，代码如下：
```  
[Tags]/**
 [Tags]* 拖动放下的时候
 [Tags]* @param y
 [Tags]*/
public void onDrop(int y) {
	// 获取放下位置在数据集合中position
	// 定义临时位置变量为了避免滑动到分割线的时候，返回-1的问题，如果为-1，则不修改dragPosition的值，急需执行，达到跳过无效位置的效果
	int tempPosition = pointToPosition(0, y);
	if (tempPosition != INVALID_POSITION) {
		dragPosition = tempPosition;
	}
	// 超出边界处理
	if (y < getChildAt(0).getTop()) {
		// 超出上边界，设为最小值位置0
		dragPosition = 0;
	} else if (y > getChildAt(getChildCount() - 1).getBottom()) {
		// 超出下边界，设为最大值位置，注意哦，如果大于可视界面中最大的View的底部则是越下界，所以判断中用getChildCount()方法
		// 但是最后一项在数据集合中的position是getAdapter().getCount()-1，这点要区分清除
		dragPosition = getAdapter().getCount() - 1;
	}
	// 数据更新
	if (dragPosition > 0 && dragPosition < getAdapter().getCount()) {
		@SuppressWarnings("unchecked")
		ArrayAdapter<String> adapter = (ArrayAdapter<String>) getAdapter();
		String dragItem = adapter.getItem(dragSrcPosition);
		// 删除原位置数据项
		adapter.remove(dragItem);
		// 在新位置插入拖动项
		adapter.insert(dragItem, dragPosition);
	}
}
```