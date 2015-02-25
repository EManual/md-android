相机越来越好，相片也越来越大，而手机应用程序所分配的内存有限，所以在读相片的时候，如果代码写得不好，经常导致OOM。信息如下：
```  
java.lang.OutOfMemoryError: bitmap size exceeds VM budget 
```
基本上要注意几个地方：
1 bitmap如果不用了，回收掉
```  
protected void onDestroy() {
	super.onDestroy();
	if (bmp1 != null) {
		bmp1.recycle();
		bmp1 = null;
	}
	if (bmp2 != null) {
		bmp2.recycle();
		bmp2 = null;
	}
}
```
2 先算出该bitmap的大小，然后通过调节采样率的方式来规避
```  
BitmapFactory.Options opts = new BitmapFactory.Options();
opts.inJustDecodeBounds = true;
BitmapFactory.decodeFile(imageFile, opts);
opts.inSampleSize = computeSampleSize(opts, minSideLength,
		maxNumOfPixels);
opts.inJustDecodeBounds = false;
try {
	return BitmapFactory.decodeFile(imageFile, opts);
} catch (OutOfMemoryError err) {
}
return null; 
```
3 在进行文件传输时，最好采用压缩的方式变成byte[]再传输
```  
public static byte[] bitmap2Bytes(Bitmap bm) {
	ByteArrayOutputStream baos = new ByteArrayOutputStream();
	bm.compress(Bitmap.CompressFormat.JPEG, 90, baos);
	return baos.toByteArray();
} 
```
由dxamhm补充了三个： 图片不用即时回收，不用等到destory，软引用，jni等