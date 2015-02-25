大家看见这个标题就应该明白了，我们这个实例就是来说怎么才能让画图在屏幕上闪烁，我就遇到过这种情况，当闪烁的时候，看了一会眼睛就受不了，所以大家在开发的时候也会遇到这种情况，那么我们怎么样才能避免那，那就来看看我们这个实例，大家就应该明白了。在处理一些复杂的界面时，往往要用view，SurfaceView来自己处理画图。比如用SurfaceView来贴两张图，并控制他们左右平移，基本代码如下：
```  
Canvas c = null;
try {
	c = mSurfaceHolder.lockCanvas(null);
	if (c != null) {
		c.setDrawFilter(mFilter);
		c.drawRect(0, 0, c.getWidth(), c.getHeight(), mBGPaint); // 画背景
		c.drawBitmap(bm1, 0, 0, null);
		c.drawBitmap(bm2, bm1.getWidth(), 0, null); // 第二张图画在第一张旁边
	}
} finally {
	if (c != null) {
		mSurfaceHolder.unlockCanvasAndPost(c);
	}
}
```
控制它左右平移时，会发现屏幕非常闪烁，眼睛看着会非常累。研究以后发现，这是因为两张图是依次一张一张贴到屏幕上的，如果刷新频率高的话，会使屏幕非常的闪烁。
解决的办法其实非常简单，想起windows下开发解决画图闪烁的办法，先把要画的图先画好放在一张大的内存位图上，然后一下贴到屏幕。android其实也是一样的，上面的问题解决方法如下：
```  
final Bitmap memBm = Bitmap.createBitmap(screenWidth, screenHeight,
		Bitmap.Config.RGB_565);
final Canvas c = new Canvas(memBm);
c.setDrawFilter(mFilter);
c.drawRect(0, 0, c.getWidth(), c.getHeight(), mBGPaint); // 画背景
c.drawBitmap(bm1, 0, 0, null);
c.drawBitmap(bm2, bm1.getWidth(), 0, null); // 第二张图画在第一张旁边
Canvas render = null;
try {
	render = mSurfaceHolder.lockCanvas();
	if (render != null) {
		render.drawBitmap(memBm, 0, 0, null);
	}
} finally {
	if (render != null)
		mSurfaceHolder.unlockCanvasAndPost(render);
}
memBm.recycle(); // 记得回收内存位图
```