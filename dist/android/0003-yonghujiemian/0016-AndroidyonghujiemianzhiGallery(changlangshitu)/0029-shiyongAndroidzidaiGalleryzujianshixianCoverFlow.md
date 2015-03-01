使用过Android自带的gallery组件的人都知道，gallery实现的效果就是拖动浏览一组图片，相比iphone里也是用于拖动浏览图片的coverflow，显然逊色不少。实际上，可以通过扩展gallery，通过伪3D变换可以基本实现coverflow的效果。本文通过源代码解析这一功能的实现。具体代码作用可参照注释。
最终实现效果如下：
![img](P)  
要使用gallery，我们必须首先给其指定一个adapter。在这里，我们实现了一个自定义的ImageAdapter，为图片制作倒影效果。
传入参数为context和程序内drawable中的图片ID数组。之后调用其中的createReflectedImages()方法分别创造每一个图像的倒影效果，生成对应的ImageView数组，最后在getView()中返回。
```  
//*
  * Copyright (C) 2010 Neil Davies
  * Licensed under the Apache License, Version 2.0 (the "License");
  * you may not use this file except in compliance with the License.
  * You may obtain a copy of the License at
  * http://www.apache.org/licenses/LICENSE-2.0
  * Unless required by applicable law or agreed to in writing, software
  * distributed under the License is distributed on an "AS IS" BASIS,
  * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  * See the License for the specific language governing permissions and
  * limitations under the License.
  * 
  * This code is base on the Android Gallery widget and was Created 
  * by Neil Davies neild001 'at' gmail dot com to be a Coverflow widget
  * @author Neil Davies
  */
public class ImageAdapter extends BaseAdapter {
	int mGalleryItemBackground;
	private Context mContext;
	private Integer[] mImageIds;
	private ImageView[] mImages;
	public ImageAdapter(Context c, int[] ImageIds) {
		mContext = c;
		mImageIds = ImageIds;
		mImages = new ImageView[mImageIds.length];
	}
	public boolean createReflectedImages() {
		// The gap we want between the reflection and the original image
		final int reflectionGap = 4;
		int index = 0;
		for (int imageId : mImageIds) {
			Bitmap originalImage = BitmapFactory.decodeResource(
					mContext.getResources(), imageId);
			int width = originalImage.getWidth();
			int height = originalImage.getHeight();
			// This will not scale but will flip on the Y axis
			Matrix matrix = new Matrix();
			matrix.preScale(1, -1);
			// Create a Bitmap with the flip matrix applied to it.
			// We only want the bottom half of the image
			Bitmap reflectionImage = Bitmap.createBitmap(originalImage, 0,
					height / 2, width, height / 2, matrix, false);
			// Create a new bitmap with same width but taller to fit
			// reflection
			Bitmap bitmapWithReflection = Bitmap.createBitmap(width,
					(height + height / 2), Config.ARGB_8888);
			// Create a new Canvas with the bitmap that's big enough for
			// the image plus gap plus reflection
			Canvas canvas = new Canvas(bitmapWithReflection);
			// Draw in the original image
			canvas.drawBitmap(originalImage, 0, 0, null);
			// Draw in the gap
			Paint deafaultPaint = new Paint();
			canvas.drawRect(0, height, width, height + reflectionGap,
					deafaultPaint);
			// Draw in the reflection
			canvas.drawBitmap(reflectionImage, 0, height + reflectionGap, null);
			// Create a shader that is a linear gradient that covers the
			// reflection
			Paint paint = new Paint();
			LinearGradient shader = new LinearGradient(0,
					originalImage.getHeight(), 0,
					bitmapWithReflection.getHeight() + reflectionGap,
					0x70ffffff, 0x00ffffff, TileMode.CLAMP);
			// Set the paint to use this shader (linear gradient)
			paint.setShader(shader);
			// Set the Transfer mode to be porter duff and destination in
			paint.setXfermode(new PorterDuffXfermode(Mode.DST_IN));
			// Draw a rectangle using the paint with our linear gradient
			canvas.drawRect(0, height, width, bitmapWithReflection.getHeight()
					+ reflectionGap, paint);
			ImageView imageView = new ImageView(mContext);
			imageView.setImageBitmap(bitmapWithReflection);
			imageView.setLayoutParams(new GalleryFlow.LayoutParams(160, 240));
			// imageView.setScaleType(ScaleType.MATRIX);
			mImages[index++] = imageView;
		}
		return true;
	}
	public int getCount() {
		return mImageIds.length;
	}
	public Object getItem(int position) {
		return position;
	}
	public long getItemId(int position) {
		return position;
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		// Use this code if you want to load from resources
		/*
		  * ImageView i = new ImageView(mContext);
		  * i.setImageResource(mImageIds[position]); i.setLayoutParams(new
		  * CoverFlow.LayoutParams(350,350));
		  * i.setScaleType(ImageView.ScaleType.CENTER_INSIDE);
		  * 
		  * //Make sure we set anti-aliasing otherwise we get jaggies
		  * BitmapDrawable drawable = (BitmapDrawable) i.getDrawable();
		  * drawable.setAntiAlias(true); return i;
		  */
		return mImages[position];
	}
	 /**
	  * Returns the size (0.0f to 1.0f) of the views depending on the 'offset' to
	  * the center.
	  */
	public float getScale(boolean focused, int offset) {
		/* Formula: 1 / (2 ^ offset) */
		return Math.max(0, 1.0f / (float) Math.pow(2, Math.abs(offset)));
	}
}
```
仅仅实现了图片的倒影效果还不够，因为在coverflow中图片切换是有旋转和缩放效果的，而自带的gallery中并没有实现。因此，我们扩展自带的gallery，实现自己的galleryflow。在原gallery类中，提供了一个方法getChildStaticTransformation()以实现对图片的变换。我们通过覆写这个方法并在其中调用自定义的transformImageBitmap(“每个图片与gallery中心的距离”)方法，，即可实现每个图片做相应的旋转和缩放。其中使用了camera和matrix用于视图变换。具体可参考代码注释。
```  
public class GalleryFlow extends Gallery {
	 /**
	  * Graphics Camera used for transforming the matrix of ImageViews
	  */
	private Camera mCamera = new Camera();
	 /**
	  * The maximum angle the Child ImageView will be rotated by
	  */
	private int mMaxRotationAngle = 60;
	 /**
	  * The maximum zoom on the centre Child
	  */
	private int mMaxZoom = -120;
	 /**
	  * The Centre of the Coverflow
	  */
	private int mCoveflowCenter;
	public GalleryFlow(Context context) {
		super(context);
		this.setStaticTransformationsEnabled(true);
	}
	public GalleryFlow(Context context, AttributeSet attrs) {
		super(context, attrs);
		this.setStaticTransformationsEnabled(true);
	}
	public GalleryFlow(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		this.setStaticTransformationsEnabled(true);
	}
	 /**
	  * Get the max rotational angle of the image
	  * @return the mMaxRotationAngle
	  */
	public int getMaxRotationAngle() {
		return mMaxRotationAngle;
	}
	 /**
	  * Set the max rotational angle of each image
	  * @param maxRotationAngle
	  *            the mMaxRotationAngle to set
	  */
	public void setMaxRotationAngle(int maxRotationAngle) {
		mMaxRotationAngle = maxRotationAngle;
	}
	 /**
	  * Get the Max zoom of the centre image
	  * @return the mMaxZoom
	  */
	public int getMaxZoom() {
		return mMaxZoom;
	}
	 /**
	  * Set the max zoom of the centre image
	  * @param maxZoom
	  *            the mMaxZoom to set
	  */
	public void setMaxZoom(int maxZoom) {
		mMaxZoom = maxZoom;
	}
	 /**
	  * Get the Centre of the Coverflow
	  * @return The centre of this Coverflow.
	  */
	private int getCenterOfCoverflow() {
		return (getWidth() - getPaddingLeft() - getPaddingRight()) / 2
				+ getPaddingLeft();
	}
	 /**
	  * Get the Centre of the View
	  * @return The centre of the given view.
	  */
	private static int getCenterOfView(View view) {
		return view.getLeft() + view.getWidth() / 2;
	}
	 /**
	  * {@inheritDoc}
	  * @see setStaticTransformationsEnabled(boolean)
	  */
	protected boolean getChildStaticTransformation(View child, Transformation t) {
		final int childCenter = getCenterOfView(child);
		final int childWidth = child.getWidth();
		int rotationAngle = 0;
		t.clear();
		t.setTransformationType(Transformation.TYPE_MATRIX);
		if (childCenter == mCoveflowCenter) {
			transformImageBitmap((ImageView) child, t, 0);
		} else {
			rotationAngle = (int) (((float) (mCoveflowCenter - childCenter) / childWidth) * mMaxRotationAngle);
			if (Math.abs(rotationAngle) > mMaxRotationAngle) {
				rotationAngle = (rotationAngle < 0) ? -mMaxRotationAngle
						: mMaxRotationAngle;
			}
			transformImageBitmap((ImageView) child, t, rotationAngle);
		}
		return true;
	}
	 /**
	  * This is called during layout when the size of this view has changed. If
	  * you were just added to the view hierarchy, you're called with the old
	  * values of 0.
	  * @param w
	  *            Current width of this view.
	  * @param h
	  *            Current height of this view.
	  * @param oldw
	  *            Old width of this view.
	  * @param oldh
	  *            Old height of this view.
	  */
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		mCoveflowCenter = getCenterOfCoverflow();
		super.onSizeChanged(w, h, oldw, oldh);
	}
	 /**
	  * Transform the Image Bitmap by the Angle passed
	  * @param imageView
	  *            ImageView the ImageView whose bitmap we want to rotate
	  * @param t
	  *            transformation
	  * @param rotationAngle
	  *            the Angle by which to rotate the Bitmap
	  */
	private void transformImageBitmap(ImageView child, Transformation t,
			int rotationAngle) {
		mCamera.save();
		final Matrix imageMatrix = t.getMatrix();
		final int imageHeight = child.getLayoutParams().height;
		final int imageWidth = child.getLayoutParams().width;
		final int rotation = Math.abs(rotationAngle);
		// 在Z轴上正向移动camera的视角，实际效果为放大图片。
		// 如果在Y轴上移动，则图片上下移动；X轴上对应图片左右移动。
		mCamera.translate(0.0f, 0.0f, 100.0f);
		// As the angle of the view gets less, zoom in
		if (rotation < mMaxRotationAngle) {
			float zoomAmount = (float) (mMaxZoom + (rotation * 1.5));
			mCamera.translate(0.0f, 0.0f, zoomAmount);
		}
		// 在Y轴上旋转，对应图片竖向向里翻转。
		// 如果在X轴上旋转，则对应图片横向向里翻转。
		mCamera.rotateY(rotationAngle);
		mCamera.getMatrix(imageMatrix);
		imageMatrix.preTranslate(-(imageWidth / 2), -(imageHeight / 2));
		imageMatrix.postTranslate((imageWidth / 2), (imageHeight / 2));
		mCamera.restore();
	}
}
```
代码到这里就结束了。有兴趣的话可以自行调整里面的参数来实现更多更炫的效果。
下面是调用的示例：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.layout_gallery);
	Integer[] images = { R.drawable.img0001, R.drawable.img0030,
			R.drawable.img0100, R.drawable.img0130, R.drawable.img0200,
			R.drawable.img0230, R.drawable.img0300, R.drawable.img0330,
			R.drawable.img0354 };
	ImageAdapter adapter = new ImageAdapter(this, images);
	adapter.createReflectedImages();
	GalleryFlow galleryFlow = (GalleryFlow) findViewById(R.id.gallery_flow);
	galleryFlow.setAdapter(adapter);
}
```
PS1:
可以看出来这样实现的gallery锯齿问题比较严重。可以在createReflectedImages()使用以下代码：
```  
BitmapDrawable bd = new BitmapDrawable(bitmapWithReflection);
bd.setAntiAlias(true);
```
然后用iv.setImageDrawable(bd);
代替iv.setImageBitmap(bitmapWithReflection);
即可基本消除锯齿。
PS2:
ImageAdapter有待确定的MemoryLeak问题，貌似的Bitmap的decode方法会造成ML，使用ImageAdapter时多次旋转屏幕后会出现OOM。目前可以通过将使用完毕的bimap调用recycle()方法和设置null并及时调用system.gc()得到一些改善，但是问题并不明显。
PS3 ON PS1：
256楼说到，为什么开启抗锯齿后不明显。答案是，锯齿不可能完全消除，但开启抗锯齿后会有很大改善。
另外还说到为什么android不默认开启锯齿，以下是我的一点想法：
插值是我现在所知道的抗锯齿的算法，也就是计算像素间的相关度对其间插入中间像素以达到平滑图像边缘的效果。但这无疑会耗费了大量的运算。
虽然我没有经过测试，但是我猜测，使用antialias后图形性能至少会下降30%。
当然，在这里没有涉及到复杂的图形运算，所以开启抗锯齿不会有很明显的性能影响，但如果你在模拟器或者低端机型上测试就会发现一点问题。
PS4：
有人问到transformImageBitmap()中这俩句话是什么意思：
```  
imageMatrix.preTranslate(-(imageWidth / 2), -(imageHeight / 2));
imageMatrix.postTranslate((imageWidth / 2), (imageHeight / 2));
```
个人的理解如下：
preTranslate相当于在对图像进行任何矩阵变换前先进行preTranslate，postTranslate相反，进行所有变换后再执行postTranlate。
这俩句的意思是：在做任何变换前，先将整个图像从图像的中心点移动到原点（(0,0)点），执行变换完毕后再将图像从原点移动到之前的中心点。
如果不加这俩句，任何变换将以图像的原点为变换中心点，加了之后，任何变换都将以图像的中心点为变换中心点。
举个例子，对图像进行旋转，需要俩个参数：一个是旋转的角度，另一个是旋转中心的坐标。旋转中心的坐标影响旋转的效果。这个能明白吗？你拿一根棍子，拿着棍子的一端进行旋转和拿在棍子中间旋转，是不一样的。preTranslate和postTranslate执行后对图像本身不会有影响，影响的是对图像进行变换时的旋转轴。
说了这么多有点绕，其实就是矩阵变换的知识。
MagicK截图
![img](P)  