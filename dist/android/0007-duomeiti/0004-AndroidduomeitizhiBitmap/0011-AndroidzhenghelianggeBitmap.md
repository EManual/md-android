有时候会遇到这样的需求，将两个bitmap对象整合并保存为一张图片，代码如下：
```  
private Bitmap toConformBitmap(Bitmap background, Bitmap foreground) {
	if (background == null) {
		return null;
	}
	int bgWidth = background.getWidth();
	int bgHeight = background.getHeight();
	// int fgWidth = foreground.getWidth();
	// int fgHeight = foreground.getHeight();
	// create the new blank bitmap 创建一个新的和SRC长度宽度一样的位图
	Bitmap newbmp = Bitmap.createBitmap(bgWidth, bgHeight, Config.ARGB_8888);
	Canvas cv = new Canvas(newbmp);
	// draw bg into
	cv.drawBitmap(background, 0, 0, null);// 在 0，0坐标开始画入bg
	// draw fg into
	cv.drawBitmap(foreground, 0, 0, null);// 在 0，0坐标开始画入fg ，可以从任意位置画入
	// save all clip
	cv.save(Canvas.ALL_SAVE_FLAG);// 保存
	// store
	cv.restore();// 存储
	return newbmp;
}
```
此方法分别传入两个bitmap对象，一个为底图(背景图background)，另一个则位于其上面(前景图foreground)，若上面的bitmap是不透明的话，就会完全遮住下面的bitmap，那么保存为图片后，就只能看到位于上面的bitmap的信息，图片的大小为两个bitmap叠加的大小。
保存bitmap为一张图片：
```  
private String saveBitmap(Bitmap bitmap) {
	String  imagePath = getApplication().getFilesDir().getAbsolutePath() + "/temp.png";
	File file = new File(imagePath);
	if(file.exists()) {
		file.delete();
	}
	try{
		FileOutputStream out = new FileOutputStream(file);
		if(bitmap.compress(Bitmap.CompressFormat.PNG, 100, out)){
			out.flush();
			out.close();
		}     
	} catch (Exception e) { 
		Toast.makeText(this, "保存失败, 1).show();
		e.printStackTrace();
	}
	return imagePath;
}
```