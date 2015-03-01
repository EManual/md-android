3.百叶窗合并动画
如图所示 屏幕中若干的矩形慢慢放大的形式关闭游戏屏幕
![img](P)  
在屏幕中用双for循环绘制出若干的矩形 在游戏更新中更新矩形绘制的宽与高 直到将屏幕完全填充。这样就可以实现游戏百叶窗合并动画的效果啦。
```  
 /** 百叶窗效果利用双for循环 修改每个矩形绘制的宽度 **/
for (int i = 0; i <= (mScreenWidth / RANDOM_TYPE_0_RANGE); i++) {
	for (int j = 0; j <= (mScreenHeight / RANDOM_TYPE_0_RANGE); j++) {
		drawFillRect(mCanvas, Color.BLACK, i * RANDOM_TYPE_0_RANGE, j
				 * RANDOM_TYPE_0_RANGE, s_effRange, s_effRange);
	}
} 
```
4.滚动水纹矩形合并动画
如图所示 利用矩形的滚动实现水纹向右关闭游戏屏幕效果。
![img](P)  
大家仔细观察上图这个动画效果，其实就是4个矩形从右到左，前3个矩形的大小是固定的中间的间隙也是固定的，最左边的矩形才为真正关闭屏幕的矩形 更新游戏界面时 4个矩形同时向右方移动 前3个只移动坐标 最后一个才是真正填充的矩形。这样就可以实现滚动的水纹的效果了。
```  
 /** 水纹效果其实绘制了4个矩形 中间留一些缝隙 **/
drawFillRect(mCanvas, Color.BLACK, 0, 0, s_effRange, mScreenHeight);
drawFillRect(mCanvas, Color.BLACK, s_effRange + RANDOM_TYPE_1_SPACE1,
		0, RANDOM_TYPE_1_RANGE1, mScreenHeight);
drawFillRect(mCanvas, Color.BLACK, s_effRange + RANDOM_TYPE_1_SPACE2,
		0, RANDOM_TYPE_1_RANGE2, mScreenHeight);
drawFillRect(mCanvas, Color.BLACK, s_effRange + RANDOM_TYPE_1_SPACE3,
		0, RANDOM_TYPE_1_RANGE3, mScreenHeight);
```