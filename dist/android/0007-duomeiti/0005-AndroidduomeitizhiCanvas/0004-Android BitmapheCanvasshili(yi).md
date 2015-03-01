在Android UI开发中我们讲到的东西主要是基础和理论内容，从本次eoeAndroid将通过实例代码来演示，本次主要是Bitmap和Canvas类的使用，根据要求缩放Bitmap对象并返回新的Bitmap对象。centerToFit方法一共有4个参数，返回一个Bitmap类型，第一个参数为原始的位图对象，width和height分别为新的宽和高，而Context是用来加载资源的上下文实例。
```  
Bitmap centerToFit(Bitmap bitmap, int width, int height, Context context) {
	final int bitmapWidth = bitmap.getWidth();
	// 获取原始bitmap的宽度
	final int bitmapHeight = bitmap.getHeight();
	if (bitmapWidth < width || bitmapHeight < height) {
		int color = context.getResources().getColor(
				R.color.window_background);
		// 从资源读取背景色
		Bitmap centered = Bitmap.createBitmap(bitmapWidth < width ? width
				: bitmapWidth, bitmapHeight < height ? height
				: bitmapHeight, Bitmap.Config.RGB_565);
		centered.setDensity(bitmap.getDensity());
		Canvas canvas = new Canvas(centered);
		canvas.drawColor(color);
		// 先绘制背景色
		canvas.drawBitmap(bitmap, (width - bitmapWidth) / 2.0f,
				(height - bitmapHeight) / 2.0f, null);
		// 通过Canvas绘制Bitmap
		bitmap = centered;
	}
	return bitmap; // 返回新的bitmap
}
```
本段代码从Android2.1开始将会应用在全新的Home主屏上，同时相关的ImageView的适应屏幕大小的setScaleType(fitCenter)方法类似，仅仅是我们制定了未来的大小。GraphableButton类实现Android UI开发从Android1.6开始，系统设置中的电池使用记录提供了一种简单的自绘Button按钮演示-GraphableButton类，通过GraphableButton我们可以很清晰的了解到前几次eoeAndroid讲到的UI开发要点。
```  
public class GraphableButton extends Button { // 从Button类继承
	private static final String TAG = "GraphableButton";
	static Paint[] sPaint = new Paint[2]; // 定义两种颜色
	static {
		sPaint[0] = new Paint();
		sPaint[0].setStyle(Paint.Style.FILL);
		sPaint[0].setColor(0xFF0080FF);
		sPaint[1] = new Paint();
		sPaint[1].setStyle(Paint.Style.FILL);
		sPaint[1].setColor(0xFFFF6060);
	}
	double[] mValues;
	public GraphableButton(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
}
```