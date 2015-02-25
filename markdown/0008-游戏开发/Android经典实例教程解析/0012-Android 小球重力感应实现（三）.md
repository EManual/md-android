java代码：
```  
import android.app.Activity;
import android.content.Context;
import android.content.pm.ActivityInfo;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.Window;
import android.view.WindowManager;
import android.view.SurfaceHolder.Callback;
public class SurfaceViewAcitvity extends Activity {
	MyView mAnimView = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 全屏显示窗口
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		// 强制横屏
		setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
		// 显示自定义的游戏View
		mAnimView = new MyView(this);
		setContentView(mAnimView);
	}
	public class MyView extends SurfaceView implements Callback, Runnable,
			SensorEventListener {
		[Tags]/** 每50帧刷新一次屏幕 **/
		public static final int TIME_IN_FRAME = 50;
		[Tags]/** 游戏画笔 **/
		Paint mPaint = null;
		Paint mTextPaint = null;
		SurfaceHolder mSurfaceHolder = null;
		[Tags]/** 控制游戏更新循环 **/
		boolean mRunning = false;
		[Tags]/** 游戏画布 **/
		Canvas mCanvas = null;
		[Tags]/** 控制游戏循环 **/
		boolean mIsRunning = false;
		[Tags]/** SensorManager管理器 **/
		private SensorManager mSensorMgr = null;
		Sensor mSensor = null;
		[Tags]/** 手机屏幕宽高 **/
		int mScreenWidth = 0;
		int mScreenHeight = 0;
		[Tags]/** 小球资源文件越界区域 **/
		private int mScreenBallWidth = 0;
		private int mScreenBallHeight = 0;
		[Tags]/** 游戏背景文件 **/
		private Bitmap mbitmapBg;
		[Tags]/** 小球资源文件 **/
		private Bitmap mbitmapBall;
		[Tags]/** 小球的坐标位置 **/
		private float mPosX = 200;
		private float mPosY = 0;
		[Tags]/** 重力感应X轴 Y轴 Z轴的重力值 **/
		private float mGX = 0;
		private float mGY = 0;
		private float mGZ = 0;
		public MyView(Context context) {
			super(context);
			[Tags]/** 设置当前View拥有控制焦点 **/
			this.setFocusable(true);
			[Tags]/** 设置当前View拥有触摸事件 **/
			this.setFocusableInTouchMode(true);
			[Tags]/** 拿到SurfaceHolder对象 **/
			mSurfaceHolder = this.getHolder();
			[Tags]/** 将mSurfaceHolder添加到Callback回调函数中 **/
			mSurfaceHolder.addCallback(this);
			[Tags]/** 创建画布 **/
			mCanvas = new Canvas();
			[Tags]/** 创建曲线画笔 **/
			mPaint = new Paint();
			mPaint.setColor(Color.WHITE);
			[Tags]/** 加载小球资源 **/
			mbitmapBall = BitmapFactory.decodeResource(this.getResources(),
					R.drawable.ball);
			[Tags]/** 加载游戏背景 **/
			mbitmapBg = BitmapFactory.decodeResource(this.getResources(),
					R.drawable.bg);
			[Tags]/** 得到SensorManager对象 **/
			mSensorMgr = (SensorManager) getSystemService(SENSOR_SERVICE);
			mSensor = mSensorMgr.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
			// 注册listener，第三个参数是检测的精确度
			// SENSOR_DELAY_FASTEST 最灵敏 因为太快了没必要使用
			// SENSOR_DELAY_GAME 游戏开发中使用
			// SENSOR_DELAY_NORMAL 正常速度
			// SENSOR_DELAY_UI 最慢的速度
			mSensorMgr.registerListener(this, mSensor,
					SensorManager.SENSOR_DELAY_GAME);
		}
		private void Draw() {
			[Tags]/** 绘制游戏背景 **/
			mCanvas.drawBitmap(mbitmapBg, 0, 0, mPaint);
			[Tags]/** 绘制小球 **/
			mCanvas.drawBitmap(mbitmapBall, mPosX, mPosY, mPaint);
			[Tags]/** X轴 Y轴 Z轴的重力值 **/
			mCanvas.drawText("X轴重力值 ：" + mGX, 0, 20, mPaint);
			mCanvas.drawText("Y轴重力值 ：" + mGY, 0, 40, mPaint);
			mCanvas.drawText("Z轴重力值 ：" + mGZ, 0, 60, mPaint);
		}
		@Override
		public void surfaceChanged(SurfaceHolder holder, int format, int width,
				int height) {
		}
		@Override
		public void surfaceCreated(SurfaceHolder holder) {
			[Tags]/** 开始游戏主循环线程 **/
			mIsRunning = true;
			new Thread(this).start();
			[Tags]/** 得到当前屏幕宽高 **/
			mScreenWidth = this.getWidth();
			mScreenHeight = this.getHeight();
			[Tags]/** 得到小球越界区域 **/
			mScreenBallWidth = mScreenWidth - mbitmapBall.getWidth();
			mScreenBallHeight = mScreenHeight - mbitmapBall.getHeight();
		}
		@Override
		public void surfaceDestroyed(SurfaceHolder holder) {
			mIsRunning = false;
		}
		@Override
		public void run() {
			while (mIsRunning) {
				[Tags]/** 取得更新游戏之前的时间 **/
				long startTime = System.currentTimeMillis();
				[Tags]/** 在这里加上线程安全锁 **/
				synchronized (mSurfaceHolder) {
					[Tags]/** 拿到当前画布 然后锁定 **/
					mCanvas = mSurfaceHolder.lockCanvas();
					Draw();
					[Tags]/** 绘制结束后解锁显示在屏幕上 **/
					mSurfaceHolder.unlockCanvasAndPost(mCanvas);
				}
				[Tags]/** 取得更新游戏结束的时间 **/
				long endTime = System.currentTimeMillis();
				[Tags]/** 计算出游戏一次更新的毫秒数 **/
				int diffTime = (int) (endTime - startTime);
				[Tags]/** 确保每次更新时间为50帧 **/
				while (diffTime <= TIME_IN_FRAME) {
					diffTime = (int) (System.currentTimeMillis() - startTime);
					[Tags]/** 线程等待 **/
					Thread.yield();
				}
			}
		}
		@Override
		public void onAccuracyChanged(Sensor arg0, int arg1) {
		}
		@Override
		public void onSensorChanged(SensorEvent event) {
			mGX = event.values[SensorManager.DATA_X];
			mGY = event.values[SensorManager.DATA_Y];
			mGZ = event.values[SensorManager.DATA_Z];
			// 这里乘以2是为了让小球移动的更快
			mPosX -= mGX * 2;
			mPosY += mGY * 2;
			// 检测小球是否超出边界
			if (mPosX < 0) {
				mPosX = 0;
			} else if (mPosX > mScreenBallWidth) {
				mPosX = mScreenBallWidth;
			}
			if (mPosY < 0) {
				mPosY = 0;
			} else if (mPosY > mScreenBallHeight) {
				mPosY = mScreenBallHeight;
			}
		}
	}
}
```