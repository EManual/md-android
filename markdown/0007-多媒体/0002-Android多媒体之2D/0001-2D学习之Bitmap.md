代码如下：
```  
try {
	canvas = sfh.lockCanvas();
	if (canvas != null) {
		canvas.drawColor(Color.BLACK);
		// ----------绘制位图
		// canvas.drawBitmap(bmp, 0, 0, paint);
		// ----------旋转位图(方式1)
		// canvas.save();
		// canvas.rotate(30, bmp.getWidth()/2, bmp.getHeight()/2);
		// canvas.drawBitmap(bmp, 0, 0, paint);
		// canvas.restore();
		// canvas.drawBitmap(bmp, 100, 0, paint);
		// ----------旋转位图(方式2)
		// Matrix mx = new Matrix();
		// mx.postRotate(30, bmp.getWidth() / 2, bmp.getHeight() / 2);
		// canvas.drawBitmap(bmp, mx, paint);
		// ----------平移位图(方式1)
		// canvas.save();
		// canvas.translate(10, 10);
		// canvas.drawBitmap(bmp, 0, 0, paint);
		// canvas.restore();
		// ----------平移位图(方式2)
		// Matrix maT = new Matrix();
		// maT.postTranslate(10, 10);
		// canvas.drawBitmap(bmp, maT, paint);
		// ----------缩放位图(方式1)
		// canvas.save();
		// canvas.scale(2f, 2f, 50 + bmp.getWidth() / 2, 50 +
		// bmp.getHeight() / 2);
		// canvas.drawBitmap(bmp, 50, 50, paint);
		// canvas.restore();
		// canvas.drawBitmap(bmp, 50, 50, paint);
		// ----------缩放位图(方式2)
		// Matrix maS = new Matrix();
		// maS.postTranslate(50, 50);
		// maS.postScale(2f, 2f, 50 + bmp.getWidth() / 2, 50 +
		// bmp.getHeight() / 2);
		// canvas.drawBitmap(bmp, maS, paint);
		// canvas.drawBitmap(bmp, 50, 50, paint);
		// ----------镜像反转位图(方式1)
		// X轴镜像
		// canvas.drawBitmap(bmp, 0, 0, paint);
		// canvas.save();
		// canvas.scale(-1, 1, 100 + bmp.getWidth() / 2, 100 +
		// bmp.getHeight() / 2);
		// canvas.drawBitmap(bmp, 100, 100, paint);
		// canvas.restore();
		// Y轴镜像
		// canvas.drawBitmap(bmp, 0, 0, paint);
		// canvas.save();
		// canvas.scale(1, -1, 100 + bmp.getWidth() / 2, 100 +
		// bmp.getHeight() / 2);
		// canvas.drawBitmap(bmp, 100, 100, paint);
		// canvas.restore();
		// ----------镜像反转位图(方式2)
		// X轴镜像
		canvas.drawBitmap(bmp, 0, 0, paint);
		Matrix maMiX = new Matrix();
		maMiX.postTranslate(100, 100);
		maMiX.postScale(-1, 1, 100 + bmp.getWidth() / 2,
				100 + bmp.getHeight() / 2);
		canvas.drawBitmap(bmp, maMiX, paint);
		// Y轴镜像
		canvas.drawBitmap(bmp, 0, 0, paint);
		Matrix maMiY = new Matrix();
		maMiY.postTranslate(100, 100);
		maMiY.postScale(1, -1, 100 + bmp.getWidth() / 2,
				100 + bmp.getHeight() / 2);
		canvas.drawBitmap(bmp, maMiY, paint);
	}
} catch (Exception e) {
	// TODO: handle exception
} finally {
	if (canvas != null)
		sfh.unlockCanvasAndPost(canvas);
}
```