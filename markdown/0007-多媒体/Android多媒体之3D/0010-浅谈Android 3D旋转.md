我们今天就来说说android中的3D效果，那么android都用到哪些东西才能来实现一个3D的效果那，其实android中的3D效果是用animation配合camera就可以实现在apidemo里就有这样的实例我们首先做一个继承animation的类Rotate3d.java。
```  
public class Rotate3d extends Animation {
	private float mFromDegree;
	private float mToDegree;
	private float mCenterX;
	private float mCenterY;
	private float mLeft;
	private float mTop;
	private Camera mCamera;
	private static final String TAG = "Rotate3d";
	public Rotate3d(float fromDegree, float toDegree, float left, float top,
			float centerX, float centerY) {
		this.mFromDegree = fromDegree;
		this.mToDegree = toDegree;
		this.mLeft = left;
		this.mTop = top;
		this.mCenterX = centerX;
		this.mCenterY = centerY;

	}
	@Override
	public void initialize(int width, int height, int parentWidth,
			int parentHeight) {
		super.initialize(width, height, parentWidth, parentHeight);
		mCamera = new Camera();
	}
	@Override
	protected void applyTransformation(float interpolatedTime, Transformation t) {
		final float FromDegree = mFromDegree;
		float degrees = FromDegree + (mToDegree - mFromDegree) * interpolatedTime;
		final float centerX = mCenterX;
		final float centerY = mCenterY;
		final Matrix matrix = t.getMatrix();
		if (degrees <= -76.0f) {
			degrees = -90.0f;
			mCamera.save();
			mCamera.rotateY(degrees);
			mCamera.getMatrix(matrix);
			mCamera.restore();
		} else if (degrees >= 76.0f) {
			degrees = 90.0f;
			mCamera.save();
			mCamera.rotateY(degrees);
			mCamera.getMatrix(matrix);
			mCamera.restore();
		} else {
			mCamera.save();
			// 这里很重要哦。
			mCamera.translate(0, 0, centerX);
			mCamera.rotateY(degrees);
			mCamera.translate(0, 0, -centerX);
			mCamera.getMatrix(matrix);
			mCamera.restore();
		}
		matrix.preTranslate(-centerX, -centerY);
		matrix.postTranslate(centerX, centerY);
	}
}
```
有了这个类一切都会变得简单的，接着只要在activity中写两个Rotate3d的对象，让两个view,分别做这两个对象的animation就好了;
```  
//下面两句很关键哦， 
Rotate3d leftAnimation = new Rotate3d(-0, -90, 0, 0, mCenterX, mCenterY); 
Rotate3d rightAnimation = new Rotate3d(-0+90, -90+90, 0.0f, 0.0f, mCenterX, mCenterY); 
leftAnimation.setFillAfter(true); 
leftAnimation.setDuration(1000); 
rightAnimation.setFillAfter(true); 
rightAnimation.setDuration(1000); 
mImageView1.startAnimation(leftAnimation); 
mImageView2.startAnimation(rightAnimation);
```
最后就是还要写一下xml.
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <FrameLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <ImageView
            android:id="@+id/image1"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:src="@drawable/image1" />
        <ImageView
            android:id="@+id/image2"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontalv
            android:background="ffff0000"
            android:src="@drawable/image2" />
    </FrameLayout>
</LinearLayout>
```