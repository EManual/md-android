很多朋友刚刚开始学习Android平台，对于Drawable、Bitmap、Canvas和Paint它们之间的概念不是很清楚，其实它们除了Drawable外早在Sun的J2ME中就已经出现了，但是在Android平台中，Bitmap、Canvas相关的都有所变化。
首先让我们理解下Android平台中的显示类是View，但是还提供了底层图形类android.graphics，今天所说的这些均为graphics底层图形接口。
Bitmap-称作位图，一般位图的文件格式后缀为bmp，当然编码器也有很多如RGB565、RGB888。作为一种逐像素的显示对象执行效率高，但是缺点也很明显存储效率低。我们理解为一种存储对象比较好。
Drawable-作为Android平下通用的图形对象，它可以装载常用格式的图像，比如GIF、PNG、JPG，当然也支持BMP，当然还提供一些高级的可视化对象，比如渐变、图形等。
Canvas-名为画布，我们可以看作是一种处理过程，使用各种方法来管理Bitmap、GL或者Path路径，同时它可以配合Matrix矩阵类给图像做旋转、缩放等操作，同时Canvas类还提供了裁剪、选取等操作。
Paint - 我们可以把它看做一个画图工具，比如画笔、画刷。他管理了每个画图工具的字体、颜色、样式。
如果涉及一些android游戏开发、显示特效可以通过这些底层图形类来高效实现自己的应用。
如在View中画一个图形：
```  
@Override
protected void onDraw(Canvas canvas) {
	super.onDraw(canvas);
	Paint paint = new Paint();
	paint.setColor(Color.RED);
	// 直接画一条直线，简单
	canvas.drawLine(0, 0, 50, 50, paint);
	// 加载图片资源文件
	Resources r = this.getContext().getResources();
	Drawable drawale = r.getDrawable(R.drawable.yellowstar);
	// 创建内存中的一张图片
	Bitmap bitmap = Bitmap.createBitmap(12, 12, Bitmap.Config.ARGB_8888);
	// 图片画片
	Canvas cas = new Canvas(bitmap);
	drawale.setBounds(0, 0, 12, 12);
	// 图片加载到bitmap上
	drawale.draw(cas);
	// 画到View上
	canvas.drawBitmap(bitmap, 30, 80, paint);
}
```