1.canvas使用本地drawable图片资源重绘的话，能够显示正确的图片，如下：
![img](P)  
2.canvas使用从网上下载下来的图片资源画图的话，图片都能显示但是覆盖掉了背景图，
如下面：
再进一步，滑动我的gridview，也就是实现了canvas重绘，当我返回再看这些图时，背景图显示出来了，但是
load的图又消失了！或者说，它们没有消失，但就是没有正常显示！
![img](P)  
现贴出我的绘图代码：
```  
public Bitmap drawMyPic(Bitmap background, Bitmap front,String groupName)  
{  
	// 另外创建一张图片   
	Bitmap newb = itmap.createBitmap(background.getWidth(), background.getHeight(), Config.ARGB_8888);// 创建一个新的和background长度宽度一样的位图
	Canvas canvas = new Canvas(newb);
	canvas.drawBitmap(background, 0, 0, null);// 在 0，0坐标开始画入原图片background
	canvas.drawBitmap(front, (background.getWidth() - front.getWidth()) / 2, (background.getHeight() - front.getHeight()) / 2, null); // 涂鸦图片画到原图片中间位置
	canvas.save(Canvas.ALL_SAVE_FLAG);
	canvas.restore();
	front.recycle();
	front = null; //一定记得回收，不然以后会很占资源
	return newb;  
}
```
我的图片们是包裹在gridview之下的，朋友们有的说是当我的图片还没有down下来的时候，canvas已经开始画图了，但是我认为没有load下来的话，canvas没有资源是画不了的，而且本地图片的话大小刚好居中，不知道为什么同样大小的图片从网上load下来的话，canvas画的时候就会覆盖掉后面的背景图。