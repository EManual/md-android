在Android中图形的旋转和变化提供了方便的矩阵Maxtrix类，Maxtrix类的setRotate方法接受图形的变换角度和缩放，最终Bitmap类的createBitmap方法中其中的重载函数，可以接受Maxtrix对象，方法原型如下
```  
public static Bitmap createBitmap (Bitmap source, int x, int y, int width, int height, Matrix m, boolean filter)
```
参数的具体意思
```  
source 源 bitmap对象
x  源坐标x位置
y 源坐标y位置
width  宽度
height  高度
```
m  接受的maxtrix对象，如果没有可以设置为null
filter 该参数仅对maxtrix包含了超过一个翻转才有效。
下面给大家一个比较经典的例子，rotate方法是静态方法可以直接调用，参数为源Bitmap对象，参数二为旋转的角度，从 0~360，返回值为新的Bitmap对象。其中具体的宽高可以调整。
```  
public static Bitmap rotate(Bitmap b, int degrees) {
	if (degrees != 0 && b != null) {
		Matrix m = new Matrix();
		m.setRotate(degrees, (float) b.getWidth() / 2, (float) b.getHeight() / 2);
		try {
			Bitmap b2 = Bitmap.createBitmap(
					b, 0, 0, b.getWidth(), b.getHeight(), m, true);
			if (b != b2) {
				b.recycle();  //再次提示Bitmap操作完应该显示的释放
				b = b2;
			}
		} catch (OutOfMemoryError ex) {
			// 建议大家如何出现了内存不足异常，最好return 原始的bitmap对象。.
		}
	}
	return b;
}
```