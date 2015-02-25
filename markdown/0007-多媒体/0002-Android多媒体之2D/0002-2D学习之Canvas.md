代码看来很多人的，最后发现还是这段代码比较全
```  
try {
	canvas = sfh.lockCanvas();
	if (canvas != null) {
		// ----设置画布绘图无锯齿
		canvas.setDrawFilter(pfd);
		// ----利用填充画布，刷屏
		canvas.drawColor(Color.BLACK);
		// ----绘制文本
		canvas.drawText("drawText", 10, 10, paint);
		// ----绘制像素点
		canvas.drawPoint(10, 20, paint);
		// ----绘制多个像素点
		canvas.drawPoints(new float[] { 10, 30, 30, 30 }, paint);
		// ----绘制直线
		canvas.drawLine(10, 40, 50, 40, paint);
		// ----绘制多条直线
		canvas.drawLines(
				new float[] { 10, 50, 50, 50, 70, 50, 110, 50 }, paint);
		// ----绘制矩形
		canvas.drawRect(10, 60, 40, 100, paint);
		// ----绘制矩形2
		Rect rect = new Rect(10, 110, 60, 130);
		canvas.drawRect(rect, paint);
		canvas.drawRect(rect, paint);
		// ----绘制圆角矩形
		RectF rectF = new RectF(10, 140, 60, 170);
		canvas.drawRoundRect(rectF, 20, 20, paint);
		// ----绘制圆形
		canvas.drawCircle(20, 200, 20, paint);
		// ----绘制弧形
		canvas.drawArc(new RectF(150, 20, 200, 70), 0, 230, true, paint);
		// ----绘制椭圆
		canvas.drawOval(new RectF(150, 80, 180, 100), paint);
		// ----绘制指定路径图形
		Path path = new Path();
		// 设置路径起点
		path.moveTo(160, 150);
		// 路线1
		path.lineTo(200, 150);
		// 路线2
		path.lineTo(180, 200);
		// 路径结束
		path.close();
		canvas.drawPath(path, paint);
		// ----绘制指定路径图形
		Path pathCircle = new Path();
		// 添加一个圆形的路径
		pathCircle.addCircle(130, 260, 20, Path.Direction.CCW);
		// ----绘制带圆形的路径文本
		canvas.drawTextOnPath("PathText", pathCircle, 10, 20, paint);
	}
} catch (Exception e) {
	// TODO: handle exception
} finally {
	if (canvas != null)
		sfh.unlockCanvasAndPost(canvas);
}
```