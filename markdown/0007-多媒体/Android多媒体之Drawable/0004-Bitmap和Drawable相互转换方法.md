很多朋友表示，不知道Android的Drawable和Bitmap之间如何相关转换。下面eoe给大家两种比较简单高效的方法。
#### 一、Bitmap转Drawable
```  
Bitmap bm=xxx; //xxx根据你的情况获取
BitmapDrawable bd=BitmapDrawable(bm);
```
提示因为BtimapDrawable是Drawable的子类，最终直接使用bd对象即可。
#### 二、 Drawable转Bitmap
转成Bitmap对象后，可以将Drawable对象通过Android的SK库存成一个字节输出流，最终还可以保存成为jpg和png的文件。
```  
Drawable d=xxx; //xxx根据自己的情况获取drawable
BitmapDrawable bd = (BitmapDrawable) d;
Bitmap bm = bd.getBitmap();
```
最终bm就是我们需要的Bitmap对象了。