在android中要实现3d翻转效果，可以通过animation配合camera实现。在api demo中有个例子。
首先需要写一个类继承animation--Rotate3DAnimation
```  
import android.view.animation.Animation;
import android.view.animation.Transformation;
import android.graphics.Camera;
import android.graphics.Matrix;
public class Rotate3dAnimation extends Animation {
	private final float mFromDegrees;
	private final float mToDegrees;
	private final float mCenterX;
	private final float mCenterY;
	private final float mDepthZ;
	private final boolean mReverse;
	private Camera mCamera;
	public Rotate3dAnimation(float fromDegrees, float toDegrees, float centerX,
			float centerY, float depthZ, boolean reverse) {
		mFromDegrees = fromDegrees;
		mToDegrees = toDegrees;
		mCenterX = centerX;
		mCenterY = centerY;
		mDepthZ = depthZ;
		mReverse = reverse;
	}
	@Override
	public void initialize(int width, int height, int parentWidth,
			int parentHeight) {
		super.initialize(width, height, parentWidth, parentHeight);
		mCamera = new Camera();
	}
	@Override
	protected void applyTransformation(float interpolatedTime, Transformation t) {
		final float fromDegrees = mFromDegrees;
		float degrees = fromDegrees
				+ ((mToDegrees - fromDegrees) * interpolatedTime);
		final float centerX = mCenterX;
		final float centerY = mCenterY;
		final Camera camera = mCamera;
		final Matrix matrix = t.getMatrix();
		camera.save();
		if (mReverse) {
			camera.translate(0.0f, 0.0f, mDepthZ * interpolatedTime);
		} else {
			camera.translate(0.0f, 0.0f, mDepthZ * (1.0f - interpolatedTime));
		}
		camera.rotateY(degrees);
		camera.getMatrix(matrix);
		camera.restore();
		matrix.preTranslate(-centerX, -centerY);
		matrix.postTranslate(centerX, centerY);
	}
}
```
mFromDegrees是开始转动的角度，mToDegrees是转停时的角度，mCenterX、mCenterY是转动时的中心坐标点。mDepthZ是转动时的深度。
怎样实现翻牌效果呢？ 这里我们假设你有两张图片（或者两个view），一张正面，一张反面。你把这两张图片布局到帧布局中 如下：
```  
<FrameLayout android:id="@+id/container"
	android:layout_width="fill_parent"
	android:layout_height="fill_parent">
	<ImageView android:id="@+id/imageview1"
		android:layout_width="fill_parent"
		android:layout_height="fill_parent"
		android:src="@drawable/front"
		android:scaleType="fitXY"/>
	<ImageView android:id="@+id/imageview2"
		android:layout_width="fill_parent"
		android:layout_height="fill_parent"
		android:src="@drawable/back"
		android:scaleType="fitXY"
		android:visibility="gone"/>
</FrameLayout>
```
然后在activity中去翻转这个framelayout，并控制当前显示哪个imageview。
整个翻转过程分为两部分，首先将framelayout从0转到90度，并设置监听器AnimationListener。在onAnimationEnd中把第一个imageview设置成GONE，把第二个imageview设置成VISIBLE，并执行接下来的翻转。把他从-90度转到0度。（注意如果这里从90度转到180度，那么第二张图片看到的是左右相反的图片）
mContainer是上面帧布局中的Framelayout
```  
final float centerX = mContainer.getWidth() / 2.0f;
final float centerY = mContainer.getHeight() / 2.0f;
Rotate3dAnimation rotation = new Rotate3dAnimation(start, end, centerX,
		centerY, 200.0f, true);
rotation.setDuration(500);
rotation.setInterpolator(new AccelerateInterpolator());
rotation.setAnimationListener(new AnimationListener() {
	@Override
	public void onAnimationEnd(Animation arg0) {
		mContainer.post(new Runnable() {
			@Override
			public void run() {
				if (viewId == R.id.imageview1) {
					imageView1.setVisibility(View.GONE);
					imageView2.setVisibility(View.VISIBLE);
				} else if (viewId == R.id.imageview2) {
					imageView2.setVisibility(View.GONE);
					imageView1.setVisibility(View.VISIBLE);
				}
				Rotate3dAnimation rotatiomAnimation = new Rotate3dAnimation(
						-90, 0, centerX, centerY, 200.0f, false);
				rotatiomAnimation.setDuration(500);
				rotatiomAnimation
						.setInterpolator(new DecelerateInterpolator());

				mContainer.startAnimation(rotatiomAnimation);
			}
		});
	}
	@Override
	public void onAnimationRepeat(Animation arg0) {
	}
	@Override
	public void onAnimationStart(Animation arg0) {
	}
});
mContainer.startAnimation(rotation);
```
下面是效果图及demo：

![img](http://emanual.github.io/md-android/img/media_3d/12_3d.jpg)
![img](http://emanual.github.io/md-android/img/media_3d/12_3d2.jpg)
![img](http://emanual.github.io/md-android/img/media_3d/12_3d3.jpg)
![img](http://emanual.github.io/md-android/img/media_3d/12_3d4.jpg)
![img](P)  