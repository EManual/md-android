我们先来看看效果图：
![img](P)  
```  
import android.content.Context;
import android.graphics.Camera;
import android.graphics.Matrix;
import android.util.AttributeSet;
import android.util.Log;
import android.view.View;
import android.view.animation.Transformation;
import android.widget.Gallery;
import android.widget.ImageView;
public class GalleryFlow extends Gallery {
	/* 图形的照相机ImageViews用于转换矩阵的 */
	private Camera mCamera = new Camera();
	 /** 这个的最大角ImageView将旋转 */
	private int mMaxRotationAngle = 60;
	 /** 这个研究中心的最大放大效果 */
	private int mMaxZoom = -120;
	/* Coverflow的中心 */
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
	  * 旋转角度得到最大的形象
	  */
	public int getMaxRotationAngle() {
		return mMaxRotationAngle;
	}
	 /**
	  * 设置它的最大旋转角度的每一个图像
	  */
	public void setMaxRotationAngle(int maxRotationAngle) {
		mMaxRotationAngle = maxRotationAngle;
	}
	public int getMaxZoom() {
		return mMaxZoom;
	}
	public void setMaxZoom(int maxZoom) {
		mMaxZoom = maxZoom;
	}
	private int getCenterOfCoverflow() {
		// Log.e("CoverFlow Width+Height", getWidth() + "*" + getHeight());
		return (getWidth() - getPaddingLeft() - getPaddingRight()) / 2
		+ getPaddingLeft();
	}
	private static int getCenterOfView(View view) {
		/*
		  * Log.e("ChildView Width+Height", view.getWidth() + " *"
		  * + view.getHeight());
		  */
		return view.getLeft() + view.getWidth() / 2;
	}
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
				rotationAngle = (rotationAngle < 0) ? -mMaxRotationAngle : mMaxRotationAngle;
			}
			transformImageBitmap((ImageView) child, t, rotationAngle);
		}
		return true;
	}
	 /**
	  * 这就是所谓的在大小的布局时,这一观点已经发生了改变。如果
	  * 你只是添加到视图层次,有人叫你旧的观念
	  * 值为0.
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
	  *            以旋转角度的位图
	  */
	private void transformImageBitmap(ImageView child, Transformation t, int rotationAngle) {
		mCamera.save();
		final Matrix imageMatrix = t.getMatrix();
		final int imageHeight = child.getLayoutParams().height;
		final int imageWidth = child.getLayoutParams().width;
		final int rotation = Math.abs(rotationAngle);
		// 在Z轴上正向移动camera的视角，实际效果为放大图片。
		// 如果在Y轴上移动，则图片上下移动；X轴上对应图片左右移动。
		mCamera.translate(0.0f, 0.0f, 100.0f);
		// 如视图的角度更少,放大
		if (rotation < mMaxRotationAngle) {
			float zoomAmount = (float) (mMaxZoom + (rotation * 1.5));
			mCamera.translate(0.0f, 0.0f, zoomAmount);
		}
		// 在Y轴上旋转，对应图片竖向向里翻转。
		// 如果在X轴上旋转，则对应图片横向向里翻转。
		mCamera.rotateY(rotationAngle);
		mCamera.getMatrix(imageMatrix);
		// Preconcats matrix相当于右乘矩阵，Postconcats matrix相当于左乘矩阵。
		imageMatrix.preTranslate(-(imageWidth / 2), -(imageHeight / 2));
		imageMatrix.postTranslate((imageWidth / 2), (imageHeight / 2));
		mCamera.restore();
	}
}
```
ImageAdapter.java
```  
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.LinearGradient;
import android.graphics.Matrix;
import android.graphics.Paint;
import android.graphics.PorterDuffXfermode;
import android.graphics.Bitmap.Config;
import android.graphics.PorterDuff.Mode;
import android.graphics.Shader.TileMode;
import android.graphics.drawable.BitmapDrawable;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
public class ImageAdapter extends BaseAdapter {
	int mGalleryItemBackground;
	private Context mContext;
	private int[] mImageIds;
	private ImageView[] mImages;
	public ImageAdapter(Context c, int[] ImageIds) {
		mContext = c;
		mImageIds = ImageIds;
		mImages = new ImageView[mImageIds.length];
	}
	public boolean createReflectedImages() {
		// 我们想要的差距之间的省思与原始图像
		final int reflectionGap = 4;
		int index = 0;
		for (int imageId : mImageIds) {
			Bitmap originalImage = BitmapFactory.decodeResource(mContext.getResources(), imageId);
			int width = originalImage.getWidth();
			int height = originalImage.getHeight();
			// 但这并不会将规模翻在Y轴上
			Matrix matrix = new Matrix();
			matrix.preScale(1, -1);
			// 创建一个位图与翻转矩阵,适用于这。
			// 我们只需要底部一半的形象
			Bitmap reflectionImage = Bitmap.createBitmap(originalImage, 0,
			height / 2, width, height / 2, matrix, false);
			// 创建一个新的位图以及相同宽度但更高的格格不入
			// 反射
			Bitmap bitmapWithReflection = Bitmap.createBitmap(width,
			(height + height / 2), Config.ARGB_8888);
			Canvas canvas = new Canvas(bitmapWithReflection);
			// 画出原始图像
			canvas.drawBitmap(originalImage, 0, 0, null);
			// 画在缝
			Paint deafaultPaint = new Paint();
			canvas.drawRect(0, height, width, height + reflectionGap, deafaultPaint);
			canvas.drawBitmap(reflectionImage, 0, height + reflectionGap, null);
			Paint paint = new Paint();
			LinearGradient shader = new LinearGradient(0, originalImage.getHeight(), 0, bitmapWithReflection.getHeight() + reflectionGap, 0x70ffffff, 0x00ffffff, TileMode.CLAMP);
			paint.setShader(shader);
			paint.setXfermode(new PorterDuffXfermode(Mode.DST_IN));
			// 画一个长方形使用油漆与我们的线性梯度
			canvas.drawRect(0, height, width, bitmapWithReflection.getHeight() + reflectionGap, paint);
			// 解决图片的锯齿现象
			BitmapDrawable bd = new BitmapDrawable(bitmapWithReflection);
			bd.setAntiAlias(true);
			ImageView imageView = new ImageView(mContext);
			// imageView.setImageBitmap(bitmapWithReflection);
			imageView.setImageDrawable(bd);
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
		/*
		  * ImageView i = new ImageView(mContext);
		  * i.setImageResource(mImageIds[position]); i.setLayoutParams(new
		  * CoverFlow.LayoutParams(350,350));
		  * i.setScaleType(ImageView.ScaleType.CENTER_INSIDE);
		  * //Make sure we set anti-aliasing otherwise we get jaggies
		  * BitmapDrawable drawable = (BitmapDrawable) i.getDrawable();
		  * drawable.setAntiAlias(true); return i;
		  */
		return mImages[position];
	}
	public float getScale(boolean focused, int offset) {
		/* Formula: 1 / (2 ^ offset) */
		return Math.max(0, 1.0f / (float) Math.pow(2, Math.abs(offset)));
	}
}
```
MainActivity.java
```  
 /**
  * 一个实现了3D效果的Gallery，就像iPhone中的相册浏览一样炫……
  */
import android.app.Activity;
import android.os.Bundle;
public class MainActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		int[] images = { R.drawable.photo1, R.drawable.photo2,
				R.drawable.photo3, R.drawable.photo4, R.drawable.photo5,
				R.drawable.photo6, R.drawable.photo7, R.drawable.photo8, };
		ImageAdapter adapter = new ImageAdapter(this, images);
		adapter.createReflectedImages();
		GalleryFlow galleryFlow = (GalleryFlow) findViewById(R.id.gallery_flow);
		galleryFlow.setAdapter(adapter);
	}
}
```
main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <com.android.CustomGallery.GalleryFlow
        android:id="@+id/gallery_flow"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```
这上面的就是我们的3D相册的应用，代码主要就是这么几个，我们要在xml里怎么布局，完事就是我们要运行的效果是怎么样的，我们的这个效果图中，我们应该要考虑一下照片旋转的最大角度，给每一个图片都设置旋转最大的角度，我们要在z轴上设置移动的视角，最后我们看Bitmap reflectionImage = Bitmap.createBitmap(originalImage, 0,height / 2, width, height / 2, matrix, false);我们在最后的一个属性当中要填上false，我们的效果就会出现了，这个地方的最爱错的，因为有的人，在写的时候都爱写true,大家要记住了。还有就是在布局的时候要定义好id。希望这个能给大家一些帮助，要是有什么地方介绍的不清楚，请大家多多的指点。