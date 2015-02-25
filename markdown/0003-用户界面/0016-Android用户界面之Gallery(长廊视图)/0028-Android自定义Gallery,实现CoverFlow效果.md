在android 实现显示文字的Gallery和Android修改Gallery页面布局的基础上，利用Gallery实现了CoverFlow效果，如下：
![img](P)  
![img](P)  
项目代码结构如下：
![img](P)  
layout_gallery.xml是Gallery的布局文件：
```  
<relativelayout 
	xmlns:android="[url=http://schemas.android.com/apk/res/android]
	http://schemas.android.com/apk/res/android[/url]
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="ffffff"
    android:orientation="vertical" >
    <com.gallery.galleryflow
        android:id="@+id/Gallery01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true
        android:spacing="-60px" />
```
android:spacing="-60px" 图片之间的间距。ActivityMain主要代码如下：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.layout_gallery);
	// 将图片做成倒影效果
	Integer[] images = { R.drawable.hawana_0, R.drawable.huanyinghei_6,
			R.drawable.wulonghui_24, R.drawable.wulonghui_28,
			R.drawable.xuanfenghei_10 };
	ImageAdapter adapter = new ImageAdapter(this, images);
	adapter.createReflectedImages();
	GalleryFlow galleryFlow = (GalleryFlow) findViewById(R.id.Gallery01);
	galleryFlow.setAdapter(adapter);
}
```
其中：
```  
ImageAdapter adapter = new ImageAdapter(this, images); 
adapter.createReflectedImages();
```
实现了图片的倒影效果，参见：android实现图片的倒影效果。GalleryFlow实现了图片的3D旋转效果：下边的方法是根据传入的角度，做图片的旋转：
```  
private void transformImageBitmap(ImageView child, Transformation t, int rotationAngle) {
	mCamera.save();
	final Matrix imageMatrix = t.getMatrix();
	final int imageHeight = child.getLayoutParams().height;
	final int imageWidth = child.getLayoutParams().width;
	final int rotation = Math.abs(rotationAngle);
	// 在Z轴上正向移动camera的视角，实际效果为放大图片。
	// 如果在Y轴上移动，则图片上下移动；X轴上对应图片左右移动。
	mCamera.translate(0.0f, 0.0f, 100.0f);
	// // As the angle of the view gets less, zoom in
	// if (rotation < mMaxRotationAngle) {
	// float zoomAmount = (float) (mMaxZoom + (rotation * 1.5));
	// //mCamera.translate(0.0f, 0.0f, zoomAmount);
	// }
	// 在Y轴上旋转，对应图片竖向向里翻转。
	// 如果在X轴上旋转，则对应图片横向向里翻转。
	mCamera.rotateY(rotationAngle);
	mCamera.getMatrix(imageMatrix);
	imageMatrix.preTranslate(-(imageWidth / 2), -(imageHeight / 2));
	imageMatrix.postTranslate((imageWidth / 2), (imageHeight / 2));
	mCamera.restore();
}
```
实现根据图片的不同位置，确定旋转角度：
```  
protected boolean getChildStaticTransformation(View child, Transformation t) {
	final int childCenter = getCenterOfView(child); 
	final int childWidth = child.getWidth(); 
	int rotationAngle = 0;
	t.clear();
	t.setTransformationType(Transformation.TYPE_MATRIX);
	Log.v("tag", "getChildStaticTransformation>>>>>>>>>>>>>>>>>>>childCenter"+childCenter+">>>>>"+Math.abs((mCoveflowCenter-childCenter)/(childWidth)));
	if (childCenter == mCoveflowCenter) { 
		transformImageBitmap((ImageView) child, t, 0);
	} else { 
		if ((mCoveflowCenter – childCenter) > 0) { 
			rotationAngle = (int) mMaxRotationAngle;
		} else { 
			rotationAngle = (int) -mMaxRotationAngle;
		} 
		if(Math.abs((mCoveflowCenter-childCenter)/(childWidth/2))==0){
			 rotationAngle = (int) (((float) (mCoveflowCenter – childCenter) /
			 childWidth) * mMaxRotationAngle);
		} 
		transformImageBitmap((ImageView) child, t, rotationAngle);
	}
	return true; 
}
```
整体效果还不是很好，并不是真正的CoverFlow，需要进一步改进。