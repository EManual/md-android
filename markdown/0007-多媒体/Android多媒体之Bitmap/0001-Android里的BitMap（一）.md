Bitmap 相关
1. Bitmap比较特别 因为其不可创建 而只能借助于BitmapFactory 而根据图像来源又可分以下几种情况：
* png图片 如：R.drawable.tianjin
```  
Bitmap bmp = BitmapFactory.decodeResource(this.getResources(), R.drawable.tianjin); //加载资源图片
```
* 图像文件 如：/sdcard/dcim/tianjin.jpeg
```  
Bitmap bmp = BitmapFactory.decodeFile("/sdcard/dcoim/tianjin.jpeg") 
```
2. Bitmap 相关应用
本地保存 即 把 Bitmap 保存在sdcard中
* 创建目标文件的File
```  
File fImage = new File("/sdcard/dcim","beijing.jpeg"); 
FileOutputStream iStream = new FileOutputStream(fImage);
```
* 取出Bitmap oriBmp
```  
oriBmp.compress(CompressFormat.JPEG, 100, iStream); 
```
参照Bitmap 的API方法 compress(Bitmap.CompressFormat format, int quality, OutputStream stream)
Write a compressed version of the bitmap to the specified outputstream.写到输出流里，就保存到文件了。
可以保存为几种格式：png，gif等貌似都可以，自己写的：
```  
public void saveMyBitmap(String bitName) throws IOException {
	File f = new File("/sdcard/Note/" + bitName + ".png");
	f.createNewFile();
	FileOutputStream fOut = null;
	try {
		fOut = new FileOutputStream(f);
	} catch (FileNotFoundException e) {
		e.printStackTrace();
	}
	mBitmap.compress(Bitmap.CompressFormat.PNG, 100, fOut);
	try {
		fOut.flush();
	} catch (IOException e) {
		e.printStackTrace();
	}
	try {
		fOut.close();
	} catch (IOException e) {
		e.printStackTrace();
	}
}
```