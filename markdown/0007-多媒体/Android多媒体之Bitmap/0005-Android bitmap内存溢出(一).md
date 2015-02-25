Android 中用bitmap 时很容易内存溢出，报如下错误：
```  
Java.lang.OutOfMemoryError : bitmap size exceeds VM budget
```
主要是加上这段：　
```  
BitmapFactory.Options options = new BitmapFactory.Options();
options.inSampleSize = 2;
BitmapFactory.Options options = new BitmapFactory.Options(); options.inSampleSize = 2;
```
eg1：(通过Uri取图片)
```  
private ImageView preview;
BitmapFactory.Options options = new BitmapFactory.Options();
options.inSampleSize = 2;//图片宽高都为原来的二分之一，即图片为原来的四分之一
Bitmap bitmap = BitmapFactory.decodeStream(cr.openInputStream(uri), null, options);
preview.setImageBitmap(bitmap);
private ImageView preview; 
BitmapFactory.Options options = new BitmapFactory.Options(); options.inSampleSize = 2;
//图片宽高都为原来的二分之一，即图片为原来的四分之一 
Bitmap bitmap = BitmapFactory.decodeStream(cr .openInputStream(uri), null, options); preview.setImageBitmap(bitmap);
```
以上代码可以优化内存溢出，但它只是改变图片大小，并不能彻底解决内存溢出。
eg2(通过路径去图片)
```  
private ImageView preview;
private String fileName= "/sdcard/DCIM/Camera/2010-05-14 16.01.44.jpg";
BitmapFactory.Options options = new BitmapFactory.Options();
options.inSampleSize = 2;//图片宽高都为原来的二分之一，即图片为原来的四分之一
Bitmap b = BitmapFactory.decodeFile(fileName, options);
preview.setImageBitmap(b);
filePath.setText(fileName);
private ImageView preview; private String fileName= "/sdcard/DCIM/Camera/2010-05-14 16.01.44.jpg"; 
BitmapFactory.Options options = new BitmapFactory.Options(); 
options.inSampleSize = 2;//图片宽高都为原来的二分之一，即图片为原来的四分之一 
Bitmap b = BitmapFactory.decodeFile(fileName, options); 
preview.setImageBitmap(b); 
filePath.setText(fileName); 
```
Android 还有一些性能优化的方法：
● 首先内存方面，可以参考 Android堆内存也可自己定义大小 和 优化Dalvik虚拟机的堆内存分配。
● 基础类型上，因为Java没有实际的指针，在敏感运算方面还是要借助NDK来完成。这点比较有意思的是Google推出NDK可能是帮助游戏开发人员，比如OpenGL ES的支持有明显的改观，本地代码操作图形界面是很必要的。
● 图形对象优化，这里要说的是Android上的Bitmap对象销毁，可以借助recycle()方法显示让GC回收一个Bitmap对象，通常对一个不用的Bitmap可以使用下面的方式，如
```  
if(bitmapObject.isRecycled()==false) //如果没有回收
	bitmapObject.recycle();
if(bitmapObject.isRecycled()==false) //如果没有回收 bitmapObject.recycle();
```
● 目前系统对动画支持比较弱智对于常规应用的补间过渡效果可以，但是对于游戏而言一般的美工可能习惯了GIF方式的统一处理，目前Android系统仅能预览GIF的第一帧，可以借助J2ME中通过线程和自己写解析器的方式来读取GIF89格式的资源。
● 对于大多数Android手机没有过多的物理按键可能我们需要想象下了做好手势识别GestureDetector和重力感应来实现操控。通常我们还要考虑误操作问题的降噪处理。