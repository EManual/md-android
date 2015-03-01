2.扇形合并动画
如图所示以扇形的转圈的形式来控制屏幕关闭
![img](P)  
在游戏更新中一直更新扇形绘制的区域 根据绘制区域的参数将扇形绘制出来 实现扇形合并的动画效果。
```  
public static void main(String[] args) {
	// rectf为扇形绘制区域 为了让扇形完全填充屏幕所以将它的区域扩大了100像素
	RectF rectf = new RectF(-RANDOM_TYPE_3_RANGE, -RANDOM_TYPE_3_RANGE,
			mScreenWidth + RANDOM_TYPE_3_RANGE, mScreenHeight
					+ RANDOM_TYPE_3_RANGE);
	// 将扇形绘制出来
	drawFillCircle(mCanvas, Color.BLACK, rectf, 0, s_effRange, true);
}
```
绘制扇形的方法
```  
 /**
  * 绘制一个扇形
  * @param canvas
  * @param color
  * @param oval
  * @param startAngle
  * @param sweepAngle
  * @param useCenter
  */
public void drawFillCircle(Canvas canvas, int color, RectF oval,
		int startAngle, int sweepAngle, boolean useCenter) {
	int backColor = mPaint.getColor();
	mPaint.setColor(color);
	canvas.drawArc(oval, startAngle, sweepAngle, useCenter, mPaint);
	mPaint.setColor(backColor);
}
```