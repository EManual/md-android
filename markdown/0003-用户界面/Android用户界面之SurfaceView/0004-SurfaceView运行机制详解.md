切入后台操作比如点击HOME按键，点击返回按键...那么重新进入程序报异常主要Surfaceiew有两点会报异常：第一：提交画布异常！如下图(模拟器错误提示，以及Logcat Detail)
效果图：
![img](P)  
解决代码：
```  
public void draw() {
	try {
		canvas = sfh.lockCanvas();
		if (canvas != null) {
			canvas.drawColor(Color.WHITE);
			canvas.drawBitmap(bmp, bmp_x, bmp_y, paint);
		}
	} catch (Exception e) {
		Log.v("Himi", "draw is Error!");
	} finally {// 备注1
		if (canvas != null)// 备注2
			sfh.unlockCanvasAndPost(canvas);
	}
}
```
下面来说另外一种情况：线程启动异常!如下图(模拟器错误提示，以及Logcat Detail)
效果图：
![img](P)  
这种异常只是在当你程序运行期间点击Home按钮后再次进入程序的时候报的异常,异常说咱们的线程已经启动！为什么返回按钮就没事？
好了，下面我们就要来先详细讲解一下Android中Back和Home按键的机制！然后分析问题，并且解决问题！
先看下面MySurfaceViewAnimation.java的类中的代码:
```  
import java.util.Vector;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.util.Log;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.view.GestureDetector.OnGestureListener;
import android.view.SurfaceHolder.Callback;
import android.view.View.OnTouchListener;
[Tags]/**
 [Tags]* @SurfaceView 运行机制详解
 [Tags]*/
public class MySurfaceViewAnimation extends SurfaceView implements Callback,
		Runnable {
	private Thread th;
	private SurfaceHolder sfh;
	private Canvas canvas;
	private Paint paint;
	private Bitmap bmp;
	private int bmp_x, bmp_y;
	public MySurfaceViewAnimation(Context context) {
		super(context);
		this.setKeepScreenOn(true);
		bmp = BitmapFactory.decodeResource(getResources(),
				R.drawable.Android_dream);
		sfh = this.getHolder();
		sfh.addCallback(this);
		paint = new Paint();
		paint.setAntiAlias(true);
		this.setLongClickable(true);
		th = new Thread(this, "Android_Thread_one");
		Log.e("Android", "surfaceChanged");
	}
	public void surfaceCreated(SurfaceHolder holder) {
		th.start();
		Log.e("Android", "surfaceCreated");
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		Log.e("Android", "surfaceChanged");
	}

	public void surfaceDestroyed(SurfaceHolder holder) {
		Log.e("Android", "surfaceDestroyed");
	}
	public void draw() {
		try {
			canvas = sfh.lockCanvas();
			if (canvas != null) {
				canvas.drawColor(Color.WHITE);
				canvas.drawBitmap(bmp, bmp_x, bmp_y, paint);
			}
		} catch (Exception e) {
			Log.v("Android", "draw is Error!");
		} finally {// 备注1
			if (canvas != null)// 备注2
				sfh.unlockCanvasAndPost(canvas);
		}
	}
	public void run() {
		while (true) {
			draw();
			try {
				Thread.sleep(100);
			} catch (Exception ex) {
			}
		}
	}
}
```
以上是我们常用的自定义SurfaceView，并且使用Runnable接口老框架了不多说了,其中我在本类的构造、创建、状态改变、消亡函数都加上打印！
这些就是我们在做Android开发时经常出现的错误，当我们出现这些错误的时候不要把自己的项目乱改起来，这样你的代码就会更乱了，到时候改也改不了。每当我们出现错误的时候我们要记住，android给我们提供了log，这个就是我们常用的找错的方法，上面的这些错误也是用log找出来的。希望这些问题对大家有用，如果有不对和不足的地方可以多多的指点。我们共同的进步。