ImageView.ScaleType共八种：
```  
1·ImageView.ScaleType.center:图片位于视图中间，但不执行缩放。
2·ImageView.ScaleType.CENTER_CROP 按统一比例缩放图片（保持图片的尺寸比例）便于图片的两维（宽度和高度）等于或者大于相应的视图的维度。
3·ImageView.ScaleType.CENTER_INSIDE按统一比例缩放图片（保持图片的尺寸比例）便于图片的两维（宽度和高度）等于或者小于相应的视图的维度。
4·ImageView.ScaleType.FIT_CENTER缩放图片使用center。
5·ImageView.ScaleType.FIT_END缩放图片使用END。
6·ImageView.ScaleType.FIT_START缩放图片使用START。
7·ImageView.ScaleType.FIT_XY缩放图片使用XY。
8·ImageView.ScaleType.MATRIX当绘制时使用图片矩阵缩放。
```
ImageView的属性android:scaleType，即ImageView.setScaleType(ImageView.ScaleType)。android:scaleType是控制图片如何resized/moved来匹对ImageView的size。ImageView.ScaleType / android:scaleType值的意义区别：
说明：以下灰色部分是一个120*200的ImageView,实验瓶则是一张48*48的图片（小于ImageView），google的logo图片是256*256的（大于ImageView）。
```  
1.CENTER /center  按图片的原来size居中显示，当图片长/宽超过View的长/宽，则截 取图片的居中部分显示
2.CENTER_CROP / centerCrop  按比例扩大图片的size居中显示，使得图片长 (宽)等于或大于View的长(宽)
3.CENTER_INSIDE / centerInside  将图片的内容完整居中显示，通过按比例缩小 或原来的size使得图片长/宽等于或小于View的长/宽
4.FIT_CENTER / fitCenter  把图片按比例扩大/缩小到View的宽度，居中显示
5.FIT_END / fitEnd   把 图片按比例扩大/缩小到View的宽度，显示在View的下部分位置
6.FIT_START / fitStart  把 图片按比例扩大/缩小到View的宽度，显示在View的上部分位置
7.FIT_XY / fitXY  把图片 不按比例 扩大/缩小到View的大小显示
8.MATRIX / matrix 用矩阵来绘制
```