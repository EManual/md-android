#### 得到网路图片
* 定义网络图片对应的BufferedInputStream
```  
//图片的链接地址 
String icoURI = "http://202.140.96.134:8080/FS-RSS/img/RN.png"; 
URL imgURL = new URL(iu); 
URLConnection conn = imgURL.openConnection(); 
conn.connect(); 
InputStream is = conn.getInputStream(); 
BufferedInputStream bis = new BufferedInputStream(is); 
```
下载之
```  
Bitmap bmp = BitmapFactory.decodeStream(bis);
```
关闭Stream
```  
bis.close(); 
is.close(); 
```
位图是我们开发中最常用的资源，毕竟一个漂亮的界面对用户是最有吸引力的。
1. 从资源中获取位图
可以使用BitmapDrawable或者BitmapFactory来获取资源中的位图。
当然，首先需要获取资源：
```  
Resources res = getResources();
```
使用BitmapDrawable获取位图
1.使用BitmapDrawable (InputStream is)构造一个BitmapDrawable；
2.使用BitmapDrawable类的getBitmap()获取得到位图；
```  
// 读取InputStream并得到位图
InputStream is = res.openRawResource(R.drawable.pic180);
BitmapDrawable bmpDraw = new BitmapDrawable(is);
Bitmap bmp = bmpDraw.getBitmap();
```
或者采用下面的方式：
```  
BitmapDrawable bmpDraw = (BitmapDrawable)res.getDrawable(R.drawable.pic180);
Bitmap bmp = bmpDraw.getBitmap();
```
使用BitmapFactory获取位图
（Creates Bitmap objects from various sources, including files, streams, and byte-arrays.）
使用BitmapFactory类decodeStream(InputStream is)解码位图资源，获取位图。
Bitmap bmp=BitmapFactory.decodeResource(res, R.drawable.pic180);
BitmapFactory的所有函数都是static，这个辅助类可以通过资源ID、路径、文件、数据流等方式来获取位图。以上方法在编程的时候可以自由选择，在Android SDK中说明可以支持的图片格式如下：png (preferred), jpg (acceptable), gif (discouraged)，和bmp（Android SDK Support Media Format）。