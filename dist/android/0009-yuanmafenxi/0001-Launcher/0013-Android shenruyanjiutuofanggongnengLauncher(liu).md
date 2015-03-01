#### 1.drop(screenX, screenY);
```  
final int[] coordinates = mCoordinatesTemp;
// 获取dropTarget对象
DropTarget dropTarget = findDropTarget((int) x, (int) y, coordinates);
// coordinates=点触点在dropTarget 中的xy坐标
if (dropTarget != null) {
	dropTarget.onDragExit(mDragSource, coordinates[0], coordinates[1],
			(int) mTouchOffsetX, (int) mTouchOffsetY, mDragView,
			mDragInfo);
	// 根据相关参数判断是否可dropTarget是否接受该drag view
	if (dropTarget.acceptDrop(mDragSource, coordinates[0],
			coordinates[1], (int) mTouchOffsetX, (int) mTouchOffsetY,
			mDragView, mDragInfo)) {
		dropTarget.onDrop(mDragSource, coordinates[0], coordinates[1],
				(int) mTouchOffsetX, (int) mTouchOffsetY, mDragView,
				mDragInfo);
		mDragSource.onDropCompleted((View) dropTarget, true);
		return true;
	} else {
		mDragSource.onDropCompleted((View) dropTarget, false);
		return true;
	}
}
return false;
```