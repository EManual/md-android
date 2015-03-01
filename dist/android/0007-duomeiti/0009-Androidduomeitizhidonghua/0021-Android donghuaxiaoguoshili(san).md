最后我们要讲的也是动画效果哦，那么把它放在最后一个讲那，因为这个动画效果是最流行的，就是现在的主流动画效果，当我说的这的时候，大家应该知道了是什么了吧，那就是3D动画效果，大家都知道现在的用户大多都喜欢大型的效果非常炫的游戏，那么我们以前做的一些小的动画效果就不能来满足用户的要求，熟话说的好，用户就是上帝，你的游戏在好，没有人玩，那也是一个失败的游戏。如果你的游戏在很多的地方都有人玩，那么你的游戏就是优秀的，说了这么多，大家一定都等的着急了，不多说了。我们来看看代码吧：
3D效果，贴上代码。Rotate3dAnimation.java
```  
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
Transition3d.java
```  
import com.example.android.apis.R;
import android.app.Activity;
import android.os.Bundle;
import android.widget.ListView;
import android.widget.ArrayAdapter;
import android.widget.AdapterView;
import android.widget.ImageView;
import android.view.View;
import android.view.ViewGroup;
import android.view.animation.Animation;
import android.view.animation.AccelerateInterpolator;
import android.view.animation.DecelerateInterpolator;
public class Transition3d extends Activity implements
		AdapterView.OnItemClickListener, View.OnClickListener {
	private ListView mPhotosList;
	private ViewGroup mContainer;
	private ImageView mImageView;
	private static final String[] PHOTOS_NAMES = new String[] { "Lyon",
			"Livermore", "Tahoe Pier", "Lake Tahoe", "Grand Canyon", "Bodie" };
	private static final int[] PHOTOS_RESOURCES = new int[] {
			R.drawable.photo1, R.drawable.photo2, R.drawable.photo3,
			R.drawable.photo4, R.drawable.photo5, R.drawable.photo6 };
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.animations_main_screen);
		mPhotosList = (ListView) findViewById(android.R.id.list);
		mImageView = (ImageView) findViewById(R.id.picture);
		mContainer = (ViewGroup) findViewById(R.id.container);
		final ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, PHOTOS_NAMES);
		mPhotosList.setAdapter(adapter);
		mPhotosList.setOnItemClickListener(this);
		mImageView.setClickable(true);
		mImageView.setFocusable(true);
		mImageView.setOnClickListener(this);
		mContainer.setPersistentDrawingCache(ViewGroup.PERSISTENT_ANIMATION_CACHE);
	}

	private void applyRotation(int position, float start, float end) {
		final float centerX = mContainer.getWidth() / 2.0f;
		final float centerY = mContainer.getHeight() / 2.0f;
		final Rotate3dAnimation rotation = new Rotate3dAnimation(start, end,
				centerX, centerY, 310.0f, true);
		rotation.setDuration(500);
		rotation.setFillAfter(true);
		rotation.setInterpolator(new AccelerateInterpolator());
		rotation.setAnimationListener(new DisplayNextView(position));
		mContainer.startAnimation(rotation);
	}
	public void onItemClick(AdapterView parent, View v, int position, long id) {
		mImageView.setImageResource(PHOTOS_RESOURCES[position]);
		applyRotation(position, 0, 90);
	}
	public void onClick(View v) {
		applyRotation(-1, 180, 90);
	}
	private final class DisplayNextView implements Animation.AnimationListener {
		private final int mPosition;
		private DisplayNextView(int position) {
			mPosition = position;
		}
		public void onAnimationStart(Animation animation) {
		}
		public void onAnimationEnd(Animation animation) {
			mContainer.post(new SwapViews(mPosition));
		}
		public void onAnimationRepeat(Animation animation) {
		}
	}
	private final class SwapViews implements Runnable {
		private final int mPosition;
		public SwapViews(int position) {
			mPosition = position;
		}
		public void run() {
			final float centerX = mContainer.getWidth() / 2.0f;
			final float centerY = mContainer.getHeight() / 2.0f;
			Rotate3dAnimation rotation;
			if (mPosition > -1) {
				mPhotosList.setVisibility(View.GONE);
				mImageView.setVisibility(View.VISIBLE);
				mImageView.requestFocus();
				rotation = new Rotate3dAnimation(90, 180, centerX, centerY,
						310.0f, false);
			} else {
				mImageView.setVisibility(View.GONE);
				mPhotosList.setVisibility(View.VISIBLE);
				mPhotosList.requestFocus();
				rotation = new Rotate3dAnimation(90, 0, centerX, centerY,
						310.0f, false);
			}
			rotation.setDuration(500);
			rotation.setFillAfter(true);
			rotation.setInterpolator(new DecelerateInterpolator());
			mContainer.startAnimation(rotation);
		}
	}
}
```
animations_main_screen.xml文件
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <ListView
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layoutAnimation="@anim/layout_bottom_to_top_slide"
        android:persistentDrawingCache="animation|scrolling" />
    <ImageView
        android:id="@+id/picture"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scaleType="fitCenter"
        android:visibility="gone" />
</FrameLayout> 
```
layout_bottom_to_top_slide.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android"
    android:animation="@anim/slide_right"
    android:animationOrder="reverse"
    android:delay="30%" />
```
slide_right.xml文件
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="@android:integer/config_shortAnimTime"
        android:fromXDelta="-100%p"
        android:toXDelta="0" />
</set>
```
希望大家看完了这三篇文章以后，加深了对android动画效果的理解，这样我这三篇帖子就没有白发，如果大家要有什么比较好的实例，拿出来分享，好让我也看看大家是怎么理解android动画效果的。