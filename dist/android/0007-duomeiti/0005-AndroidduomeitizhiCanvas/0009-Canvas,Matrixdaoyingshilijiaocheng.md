大家都知道android系统自带的控件都不好用，想要美观立体实用肯定需要自定View(未来是自定义的世界。跑题了)，自定义view要是做出来效果肯定要Matrix和Canva以及paint，那么今天带大家去学学利用Matrix、Canvas做一个倒影的效果例子。
效果图：

![img](http://emanual.github.io/md-android/img/media_canvas/10_canvas.jpg) 

```  
 /**
  * 创建有倒影的位图
  * @param oldBitmap
  */
private Bitmap createFlectionBitmap(Bitmap oldBitmap) {
	final int gap = 2; // 老图 和倒立图的间隔距离
	// 获取老图的宽、高
	int mWidth = oldBitmap.getWidth();
	int mHeight = oldBitmap.getHeight();
	 /**
	  * 第一步:创建倒立的Bitmap，宽度跟老图一样 高度是老图的一般
	  * 第二步：画出能容纳老图、倒立图、间隔的背景Bitmap-background 第三步:创建间隔的图形 (如果你不喜欢，也可以不花)
	  * 第四部：用backgroud传给canvas 然后安顺序在background上 画出老图、间隔(用矩形)、倒立的图 第五部：渐变处理用
	  * LinearGradient 第六步：用一个矩形覆盖在倒立的像上,这样在渐变的效果下使得看起来像倒影一样 大功告成
	  */
	// 第一步：画倒立的Bitmap 宽度跟老图一样 高度是原图的一般
	Matrix matrix = new Matrix();
	matrix.preScale(1, -1); // 1表示翻转x的一倍 -1表示翻转拉伸-1倍 也就是倒立过来
	Bitmap flectionBitmap = Bitmap.createBitmap(oldBitmap, 0, mHeight / 2,
			mWidth, mHeight / 2, matrix, false);
	// 第二步：画出完整图像的背景Bitmap. 注解 创建背景位图 宽度当时是要跟老图的一样 高度呢应该是 背景高 = 老图的高 + 倒立图高
	// + 间隔 。 当时是创建最好质量的背景位图了
	Bitmap background = Bitmap.createBitmap(mWidth, mHeight + mHeight / 2
			+ mHeight + gap, Config.ARGB_8888);
	Canvas canvas = new Canvas(background);
	// 为什么要将background传递给Canvas呢
	// 因为我们要在background上画图
	// 所以要讲给canvas
	Paint paintRect = new Paint();
	// 第三步、第四步：将间距、老图、倒立图画在背景上
	canvas.drawBitmap(oldBitmap, 0, 0, null);
	canvas.drawRect(0, mHeight, mWidth, mHeight + gap, paintRect);
	canvas.drawBitmap(flectionBitmap, 0, mHeight + gap, paintRect);
	Paint shaderPaint = new Paint();
	// 从(0,mHeight) 到 (0,flectionBitmap.getHeight())
	// 开始渐变,颜色从0x70ffffff到0x00ffffff,利用镜像模式
	LinearGradient shader = new LinearGradient(0, mHeight, 0,
			flectionBitmap.getHeight(), 0x70ffffff, 0x00ffffff,
			TileMode.MIRROR);
	shaderPaint.setShader(shader);
	shaderPaint.setXfermode(new PorterDuffXfermode(
			android.graphics.PorterDuff.Mode.DST_IN));
	canvas.drawRect(0, mHeight, mWidth, background.getHeight(), shaderPaint);
	return background;
}
```