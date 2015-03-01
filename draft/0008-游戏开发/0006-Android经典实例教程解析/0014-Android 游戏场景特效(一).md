大家在玩游戏的时候应该有时候会发现在切换游戏场景的时候 界面会播放一段非常好看的动画 比如一个百叶窗的形式关闭界面 然后在打开界面 效果非常好看 用户体验也非常好，今天我向大家解读游戏开发中常用的四种切换场景的特效动画。
下面游戏界面中 红框内标识了4个图片按钮 分别点击这4个按钮会分别播放4组切换场景的特效动画。
![img](P)  
1.交叉相合动画
左右两边分别以若干个矩形以交替相合的形式合并 控制屏幕关闭
![img](P)  
通过两个for循环 1 3 5 7 9 绘制屏幕左方矩形 2 4 6 8 10 绘制屏幕右放矩形，在游戏更新中计算矩形移动的坐标，然后左边的矩形分别向右延伸。右边的矩形分别向左延伸，这样就可以实现矩形的交叉合并动画。
```  
 /** 交错的实现矩形相交 **/
int count = (mScreenHeight / RANDOM_TYPE_2_RANGE);
for (int i = 0; i < count; i += 2) {
	drawFillRect(mCanvas, Color.BLACK, 0, i * RANDOM_TYPE_2_RANGE,
			s_effRange, RANDOM_TYPE_2_RANGE);
}
for (int i = 1; i < count; i += 2) {
	drawFillRect(mCanvas, Color.BLACK, mScreenWidth - s_effRange, i
			 * RANDOM_TYPE_2_RANGE, s_effRange, RANDOM_TYPE_2_RANGE);
}
```
绘制矩形的方法
```  
 /**
  * 绘制一个矩形
  * @param canvas
  * @param color
  * @param x
  * @param y
  * @param w
  * @param h
  */
public void drawFillRect(Canvas canvas, int color, int x, int y, int w,
		int h) {
	int backColor = mPaint.getColor();
	mPaint.setColor(color);
	canvas.drawRect(x, y, x + w, y + h, mPaint);
	mPaint.setColor(backColor);
}
```