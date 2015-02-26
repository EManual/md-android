我们现在说的这个类是非常重要的，不管你做什么都都得用到Bitmap类，我们就来看看，Bitmap类都有哪些是必须要掌握的，Paint类是我们必须要掌握的。p.setAlpha(0x80);括号里的0x00表示的就是完全透明。要是0xff那就表示不透明。ascent 表示到基准线之上的距离、descent 表示到基准线之下的距离，下面我们就具体了解一下Bitmap类。
```  
private static class SampleView extends View {
	private Bitmap mBitmap;
	private Bitmap mBitmap2;
	private Bitmap mBitmap3;
	private Shader mShader;
	private static void drawIntoBitmap(Bitmap bm) {
		float x = bm.getWidth();
		float y = bm.getHeight();
		Canvas c = new Canvas(bm);
		Paint p = new Paint();
		/* Paint类的一个边缘光滑的方法，true表示边缘光滑 */
		p.setAntiAlias(true);
		p.setAlpha(0x80);// 设置颜色透明度为十六进制80(半透明),0x00全透明，0xFF不透明
		/* 在位图矩阵区域内画一个相切的圆 */
		c.drawCircle(x / 2, y / 2, x / 2, p);
		p.setAlpha(0x30);
		/*
		  * 用指定的PorterDuff模型创建xformode，PorterDuff.Mode.SRC 表示下面要绘制的文本应在上面绘制的圆的上层
		  */
		p.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC));
		p.setTextSize(60);
		/*
		  * Paint.Align 是文本对齐方式的一个枚举类 CENTER表示文本居中 LEFT 表示做对齐 RIGHT 表示右对齐
		  */
		p.setTextAlign(Paint.Align.CENTER);
		Paint.FontMetrics fm = p.getFontMetrics();
		c.drawText("Alpha", x / 2, (y - fm.ascent) / 2, p);
	}
	public SampleView(Context context) {
		super(context);
		setFocusable(true);
		/* 取得资源文件的输入流 */
		InputStream is = context.getResources().openRawResource(
				R.drawable.app_sample_code);
		/*
		  * BitmapFactory 是位图的一个工厂类 从各种各样的位图对象中创建位图对象，包括文件，流，字节数组。
		  */
		mBitmap = BitmapFactory.decodeStream(is);
		/*
		  * extractAlpha()位图的这个方法是通过提取 了原始位图的透明通道值重建新的位图
		  */
		mBitmap2 = mBitmap.extractAlpha();
		/*
		  * 通过位图的宽度和高度已经位图的颜色配置来创建位图 Bitmap.Config是内部枚举类表示位图的颜色配置
		  * 它的颜色配置有ALPHA_8、ARGB_4444、ARGB_8888、RGB_565
		  */
		mBitmap3 = Bitmap.createBitmap(200, 200, Bitmap.Config.ALPHA_8);
		drawIntoBitmap(mBitmap3);
	}
}
```
我们从注释上就可以非常明白的了解Bitmap类这个类了，总之这个类在android中用到的地方非常的多，要是开发什么游戏或软件，那就不可避免要用到Bitmap类。