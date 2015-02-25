下面我们就来介绍一些特效，我们将绿色分量乘以2变为原来的2倍。相信大家至此已经明白了如何通过颜色矩阵来改变各颜色分量。下面的这一段代码来看，通过调整颜色矩阵来获得不同的颜色效果。
```  
public class MyImage extends View {
	private Paint mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
	private Bitmap mBitmap;
	private float[] array = new float[20];
	private float mAngle;
	public MyImage(Context context, AttributeSet attrs) {
		super(context, attrs);
		mBitmap = BitmapFactory.decodeResource(context.getResources(),
				R.drawable.test);
		invalidate();
	}
	public void setValues(float[] a) {
		for (int i = 0; i < 20; i++) {
			array = a;
		}
	}
	@Override
	protected void onDraw(Canvas canvas) {
		Paint paint = mPaint;
		paint.setColorFilter(null);
		canvas.drawBitmap(mBitmap, 0, 0, paint);
		ColorMatrix cm = new ColorMatrix(); // 设置颜色矩阵
		cm.set(array); // 颜色滤镜，将颜色矩阵应用于图片
		paint.setColorFilter(new ColorMatrixColorFilter(cm)); // 绘图
		canvas.drawBitmap(mBitmap, 0, 0, paint);
		Log.i("CMatrix", "--------->onDraw");
	}
}
```
我们在来看看效果图吧：
![img](P)  
![img](P)  
![img](P)  