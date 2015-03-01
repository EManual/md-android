需求：从服务器下载一张图片，显示在ImageView控件上，并将该图片保存在移动设备的SD上。
步骤：
（一）获得输入流
```  
// urlPath:服务器路径;
public InputStream getUrlInputStream(String urlPath) throws IOException {
	URL url = new URL(urlPath);
	HttpURLConnection conn = (HttpURLConnection) url.openConnection();
	InputStream in = conn.getInputStream();
	if (in != null) {
		return in;
	} else {
		Log.i("test", "输入流对象为空");
		return null;
	}
}
```
（二）将输入流转化为Bitmap流
```  
public Bitmap getMyBitmap(InputStream in) {
	Bitmap bitmap = null;
	if (in != null) {
		bitmap = BitmapFactory.decodeStream(in);
		// BitmapFactory的作用：create Bitmap objects from various
		// sources,including files,streams and byte-arrays;
		return bitmap;
	} else {
		Log.i("test", "输入流对象in为空");
		return null;
	}
}
```
（三）给ImageView对象赋值
```  
public void setWidgetImage(Bitmap bitmap) {
	ImageView img = new ImageView(this);
	if (bitmap != null) {
		img.setImageBitmap(bitmap);
	}
}
```
（四）获取SD卡上的文件存储路径
```  
public void createSDFile() {
	File sdroot = Environment.getExternalStorageDirectory();
	File file = new File(sdroot + "/Android/date/包名/文件名");
	if (Environment.MEDIA_MOUNTED.equals(Environment
			.getExternalStorageState())) {
		 /**
		  * 相关操作
		  */
	}
}
```
注意:SD卡的权限
（五）将图片保存到SD卡上
```  
public boolean readToSDCard(File file, Bitmap bitmap)
		throws FileNotFoundException {
	FileOutputStream os = new FileOutputStream(file);
	return bitmap.compress(Bitmap.CompressFormat.PNG, 90, os);
	// bitmap.compress()的作用:write a compressed version of the bitmap to the
	// specified outputstream;
	// true:表示操作成功，false:表示操作失败
}
```