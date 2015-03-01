思路如下：
首先判断SD卡是否插入-->
```  
public String getSDPath() {
	File SDdir = null;
	boolean sdCardExist = Environment.getExternalStorageState().equals(
			android.os.Environment.MEDIA_MOUNTED);
	if (sdCardExist) {
		SDdir = Environment.getExternalStorageDirectory();
	}
	if (SDdir != null) {
		return SDdir.toString();
	} else {
		return null;
	}
}
```
然后创建文件夹-->
```  
public void createSDCardDir() {
	if (getSDPath() == null) {
		Toast.makeText(PicSharesActivity.this, "未找到SD卡", 1000).show();
	} else {
		if (Environment.MEDIA_MOUNTED.equals(Environment
				.getExternalStorageState())) {
			// 创建一个文件夹对象，赋值为外部存储器的目录
			File sdcardDir = Environment.getExternalStorageDirectory();
			// 得到一个路径，内容是sdcard的文件夹路径和名字
			newPath = sdcardDir.getPath() + "/***app/tempImages/";// newPath在程序中要声明
			File path1 = new File(newPath);
			if (!path1.exists()) {
				// 若不存在，创建目录，可以在应用启动的时候创建
				path1.mkdirs();
				System.out.println("paht ok,path:" + newPath);
			}
		} else {
			System.out.println("false");
		}
	}
}
```
创建好文件夹之后就可以保存图片了-->
```  
public void saveMyBitmap(String bitName, int percent) throws IOException {
	Bitmap bmp = drawable2Bitmap(picView.getDrawable());// 这里的drawable2Bitmap方法是我把ImageView中
														// 的drawable转化成bitmap，当然实验的时候可以自己创建bitmap
	File f = new File(newPath + bitName + ".jpg");
	f.createNewFile();
	FileOutputStream fOut = null;
	try {
		fOut = new FileOutputStream(f);
	} catch (FileNotFoundException e) {
		e.printStackTrace();
	}
	bmp.compress(Bitmap.CompressFormat.JPEG, percent, fOut);
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
// 附加drawable2Bitmap方法
static Bitmap drawable2Bitmap(Drawable d) {
	int width = d.getIntrinsicWidth();
	int height = d.getIntrinsicHeight();
	Bitmap.Config config = d.getOpacity() != PixelFormat.OPAQUE ? Bitmap.Config.ARGB_8888
			: Bitmap.Config.RGB_565;
	Bitmap bitmap = Bitmap.createBitmap(width, height, config);
	Canvas canvas = new Canvas(bitmap);
	d.setBounds(0, 0, width, height);
	d.draw(canvas);
	return bitmap;
}
```