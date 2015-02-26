代码如下：
```  
import android.graphics.Camera;
import android.graphics.Matrix;
import android.view.animation.Animation;
import android.view.animation.Transformation;
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
		float degrees = FromDegree + (mToDegree - mFromDegree)
				 * interpolatedTime;
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
代码如下：
```  
import android.app.Activity;
import android.util.Log;
import android.view.MotionEvent;
import android.view.GestureDetector.OnGestureListener;
public class FlingGuest implements OnGestureListener {
	Activity activity;
	int VALUE_DISTANCE = 100;
	int VALUE_SPEED = 20;
	public FlingGuest(Activity a) {
		activity = a;
	}
	// 用户轻触触摸屏，由1个MotionEvent ACTION_DOWN触发
	public boolean onDown(MotionEvent e) {
		Log.d("TAG", "[+++++++++++][onDown]");
		return true;
	}
	// e1, the begin of ACTION_DOWN MotionEvent
	// e2, the end of ACTION_DOWN MotionEvent
	// velocityX, the motion speed in X
	// velocityY：the motion speed in y
	// 用户按下触摸屏、快速移动后松开,由1个MotionEvent ACTION_DOWN,
	// 多个ACTION_MOVE, 1个ACTION_UP触发
	// e1：第1个ACTION_DOWN MotionEvent
	// e2：最后一个ACTION_MOVE MotionEvent
	// velocityX：X轴上的移动速度，像素/秒
	// velocityY：Y轴上的移动速度，像素/秒
	// 触发条件 ：
	// X轴的坐标位移大于VALUE_DISTANCE，且移动速度大于VALUE_SPEED个像素/秒
	public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
			float velocityY) {
		if ((e1.getX() - e2.getX() > VALUE_DISTANCE)
				&& Math.abs(velocityX) > VALUE_SPEED) {
			((Layout3D) activity).leftMoveHandle();
		} else if ((e2.getX() - e1.getX() > VALUE_DISTANCE)
				&& Math.abs(velocityX) > VALUE_SPEED) {
			((Layout3D) activity).rightMoveHandle();
		}
		return true;
	}
	// 用户长按触摸屏，由多个MotionEvent ACTION_DOWN触发
	public void onLongPress(MotionEvent e) {
		Log.d("TAG", "[+++++++++++][onLongPress]");
	}
	// 用户按下触摸屏，并拖动，由1个MotionEvent ACTION_DOWN, 多个ACTION_MOVE触发
	public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX,
			float distanceY) {
		Log.d("TAG", "[+++++++++++][onScroll]");
		return true;
	}
	// 用户轻触触摸屏，尚未松开或拖动，由一个1个MotionEvent ACTION_DOWN触发
	// 注意和onDown()的区别，强调的是没有松开或者拖动的状态
	public void onShowPress(MotionEvent e) {
		Log.d("TAG", "[+++++++++++][onShowPress]");
	}
	// 用户（轻触触摸屏后）松开，由一个MotionEvent ACTION_UP触发
	public boolean onSingleTapUp(MotionEvent e) {
		Log.d("TAG", "[+++++++++++][onSingleTapUp]");
		return true;
	}
}
```