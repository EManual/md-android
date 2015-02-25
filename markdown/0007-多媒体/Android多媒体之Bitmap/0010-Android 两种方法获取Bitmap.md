第一种方法 
```  
//得到Resources对象 
Resources r = this.getContext().getResources(); 
//以数据流的方式读取资源 
Inputstream is = r.openRawResource(R.drawable.my_background_image); 
BitmapDrawable bmpDraw = new BitmapDrawable(is); 
Bitmap bmp = bmpDraw.getBitmap();
```
第二种方法这种方法是通过BitmapFactory这个工具类，BitmapFactory的所有函数都是static，这个辅助类可以通过资源ID、路径、文件、数据流等方式来获取位图。大家可以打开API 看一下里边全是静态方法。这个类里边有一个叫做 decodeStream(InputStream is)   
此方法可以 解码一个新的位图从一个InputStream。这是获得资源的InputStream。 
```  
InputStream is = getResources().openRawResource(R.drawable.icon); 
Bitmap mBitmap = BitmapFactory.decodeStream(is); 
Paint mPaint = new Paint(); 
canvas.drawBitmap(mBitmap, 40, 40, mPaint); 
```