6.图片水印的生成方法
生成水印的过程。其实分为三个环节：第一，载入原始图片；第二，载入水印图片；第三，保存新的图片。
```  
/*
  * create the bitmap from a byte array
  * @param src the bitmap object you want proecss
  * @param watermark the water mark above the src
  * @return return a bitmap object ,if paramter's length is 0,return null
  */
private Bitmap createBitmap(Bitmap src, Bitmap watermark) {
	String tag = "createBitmap";
	Log.d(tag, "create a new bitmap");
	if (src == null) {
		return null;
	}
	int w = src.getWidth();
	int h = src.getHeight();
	int ww = watermark.getWidth();
	int wh = watermark.getHeight();
	// create the new blank bitmap
	Bitmap newb = Bitmap.createBitmap(w, h, Config.ARGB_8888);
	// 创建一个新的和SRC长度宽度一样的位图
	Canvas cv = new Canvas(newb);
	// draw src into
	cv.drawBitmap(src, 0, 0, null);// 在 0，0坐标开始画入src
	// draw watermark into
	cv.drawBitmap(watermark, w - ww + 5, h - wh + 5, null);// 在src的右下角画入水印
	// save all clip
	cv.save(Canvas.ALL_SAVE_FLAG);// 保存
	// store
	cv.restore();// 存储
	return newb;
}
```
7.Canvas的save和restore
onDraw方法会传入一个Canvas对象，它是你用来绘制控件视觉界面的画布。
在onDraw方法里，我们经常会看到调用save和restore方法，它们到底是干什么用的呢？
save：用来保存Canvas的状态。save之后，可以调用Canvas的平移、放缩、旋转、错切、裁剪等操作。
restore：用来恢复Canvas之前保存的状态。防止save后对Canvas执行的操作对后续的绘制有影响。
save和restore要配对使用（restore可以比save少，但不能多），如果restore调用次数比save多，会引发Error。save和restore之间，往往夹杂的是对Canvas的特殊操作。
例如：我们先想在画布上绘制一个右向的三角箭头，当然，我们可以直接绘制，另外，我们也可以先把画布旋转90°，画一个向上的箭头，然后再旋转回来（这种旋转操作对于画圆周上的标记非常有用）。然后，我们想在右下角有个20像素的圆，那么，onDraw中的核心代码是：
```  
int px = getMeasuredWidth();
int py = getMeasuredWidth();
// Draw background
canvas.drawRect(0, 0, px, py, backgroundPaint);
canvas.save();
canvas.rotate(90, px / 2, py / 2);
// Draw up arrow
canvas.drawLine(px / 2, 0, 0, py / 2, linePaint);
canvas.drawLine(px / 2, 0, px, py / 2, linePaint);
canvas.drawLine(px / 2, 0, px / 2, py, linePaint);
canvas.restore();
// Draw circle
canvas.drawCircle(px - 10, py - 10, 10, linePaint);
```