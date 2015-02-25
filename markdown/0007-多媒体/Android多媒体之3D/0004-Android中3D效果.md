大家见过没有用opengl的3D动画，其实就是用的Camera实现的，内部机制实际上还是opengl，不过大大简化了使用方法。
Camera就像一个摄像机，一个物体在原地不动，然后我们带着这个摄像机四处移动，在摄像机里面呈现出来的画面，就会有立体感，就可以从各个角度观看这个物体。
它有旋转、平移的一系列方法，实际上都是在改变一个Matrix对象，一系列操作完毕之后，我们得到这个Matrix，然后画我们的物体，就可以了。
```  
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Camera;
import android.graphics.Canvas;
import android.graphics.Matrix;
import android.graphics.Paint;
import android.view.MotionEvent;
import android.view.View;
[Tags]/**
 [Tags]* 图片三维翻转
 [Tags]*/
public class CubeView extends View {
	// 摄像机
	private Camera mCamera;
	// 翻转用的图片
	private Bitmap face;
	private Matrix mMatrix = new Matrix();
	private Paint mPaint = new Paint();
	private int mLastMotionX, mLastMotionY;
	// 图片的中心点坐标
	private int centerX, centerY;
	// 转动的总距离，跟度数比例1:1
	private int deltaX, deltaY;
	// 图片宽度高度
	private int bWidth, bHeight;
	public CubeView(Context context) {
		super(context);
		setWillNotDraw(false);
		mCamera = new Camera();
		mPaint.setAntiAlias(true);
		face = BitmapFactory.decodeResource(getResources(), R.drawable.x);
		bWidth = face.getWidth();
		bHeight = face.getHeight();
		centerX = bWidth >> 1;
		centerY = bHeight >> 1;
	}
	[Tags]/**
	 [Tags]* 转动
	 [Tags]* @param degreeX
	 [Tags]* @param degreeY
	 [Tags]*/
	void rotate(int degreeX, int degreeY) {
		deltaX += degreeX;
		deltaY += degreeY;
		mCamera.save();
		mCamera.rotateY(deltaX);
		mCamera.rotateX(-deltaY);
		mCamera.translate(0, 0, -centerX);
		mCamera.getMatrix(mMatrix);
		mCamera.restore();
		// 以图片的中心点为旋转中心,如果不加这两句，就是以（0,0）点为旋转中心
		mMatrix.preTranslate(-centerX, -centerY);
		mMatrix.postTranslate(centerX, centerY);
		mCamera.save();
		postInvalidate();
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		int x = (int) event.getX();
		int y = (int) event.getY();
		switch (event.getAction()) {
		case MotionEvent.ACTION_DOWN:
			mLastMotionX = x;
			mLastMotionY = y;
			break;
		case MotionEvent.ACTION_MOVE:
			int dx = x - mLastMotionX;
			int dy = y - mLastMotionY;
			rotate(dx, dy);
			mLastMotionX = x;
			mLastMotionY = y;
			break;
		case MotionEvent.ACTION_UP:
			break;
		}
		return true;
	}
	@Override
	public void dispatchDraw(Canvas canvas) {
		super.dispatchDraw(canvas);
		canvas.drawBitmap(face, mMatrix, mPaint);
	}
}
```