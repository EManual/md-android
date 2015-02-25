#### 一、结构
```  
public static class Gallery.LayoutParams extends ViewGroup.LayoutParams
java.lang.Object
android.view. ViewGroup.LayoutParams
android.widget.Gallery.LayoutParams
```
#### 二、类概述
Gallery(画廊)扩展了LayoutParams，以此提供可以容纳当前的转换信息和先前的位置转换信息的场所。
#### 三、补充
```  
public View getView(int position, View convertView, ViewGroup parent) {
	ImageView imageView = new ImageView(mContext);
	// 设置当前图像的图像（position为当前图像列表的位置）
	imageView.setImageResource(resIds[position]);
	imageView.setScaleType(ImageView.ScaleType.FIT_XY);
	imageView.setLayoutParams(new Gallery.LayoutParams(163, 106));
	// 设置Gallery组件的背景风格
	imageView.setBackgroundResource(mGalleryItemBackground);
	return imageView;
}
```