各位童鞋请你们注意：surfaceview中确实有onDraw这个方法，但是你surfaceview不会自己去调用！！！
而我代码中的ondraw() 也好 draw() 也好，都是我自己定义的一个方法，放在线程中不断调用的，一定要注意！!
之前我们对view和surfaceview做了比较和取舍，最后我们发现surfaceview更加的适合运作与游戏开发中，那么下面就让我们来看看这个surfaceview的结构吧；
先上一段代码：
```  
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.SurfaceHolder.Callback;
import android.view.animation.Animation;
public class MySurfaceView extends SurfaceView implements Callback, Runnable {// 备注1
	private SurfaceHolder sfh;
	private Thread th;
	private Canvas canvas;
	private Paint paint;
	private int ScreenW, ScreenH;
	public MySurfaceView(Context context) {
		super(context);
		th = new Thread(this);
		sfh = this.getHolder();
		sfh.addCallback(this); // 备注1
		paint = new Paint();
		paint.setAntiAlias(true);
		paint.setColor(Color.RED);
		this.setKeepScreenOn(true);// 保持屏幕常亮
	}
	@Override
	public void startAnimation(Animation animation) {
		super.startAnimation(animation);
	}
	public void surfaceCreated(SurfaceHolder holder) {
		ScreenW = this.getWidth();// 备注2
		ScreenH = this.getHeight();
		th.start();
	}
	private void draw() {
		try {
			canvas = sfh.lockCanvas(); // 得到一个canvas实例
			canvas.drawColor(Color.WHITE);// 刷屏
			canvas.drawText("android", 100, 100, paint);// 画文字文本
			canvas.drawText("这就是简单的一个游戏框架", 100, 130, paint);
			sfh.unlockCanvasAndPost(canvas); // 将画好的画布提交
		} catch (Exception ex) {
		} finally { // 备注3
			if (canvas != null)
				sfh.unlockCanvasAndPost(canvas);
		}
	}
	public void run() {
		while (true) {
			draw();
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}

	public void surfaceDestroyed(SurfaceHolder holder) {
	}
}
```
代码很简单，我们继承继承surfaceview类，并且使用回调callback接口以及线程runnable接口。那么这里我简单的说下Callback接口和SurfaceHolder 类的作用；
#### 备注1
callback接口:
只要继承SurfaceView类并实现SurfaceHolder.Callback接口就可以实现一个自定义的SurfaceView了，SurfaceHolder.Callback在底层的Surface状态发生变化的时候通知View，SurfaceHolder.Callback具有如下的接口：
surfaceCreated(SurfaceHolder holder)：当Surface第一次创建后会立即调用该函数。程序可以在该函数中做些和绘制界面相关的初始化工作，一般情况下都是在另外的线程来绘制界面，所以不要在这个函数中绘制Surface。
surfaceChanged(SurfaceHolder holder, int format, int width,int height)：当Surface的状态（大小和格式）发生变化的时候会调用该函数，在surfaceCreated调用后该函数至少会被调用一次。
SurfaceHolder类：
它是一个用于控制surface的接口，它提供了控制surface 的大小，格式，上面的像素，即监视其改变的。
SurfaceView的getHolder()函数可以获取SurfaceHolder对象，Surface就在SurfaceHolder对象内。虽然Surface保存了当前窗口的像素数据，但是在使用过程中是不直接和Surface打交道的，由SurfaceHolder的Canvas lockCanvas()或则Canvas lockCanvas()函数来获取Canvas对象，通过在Canvas上绘制内容来修改Surface中的数据。如果Surface不可编辑或则尚未创建调用该函数会返回null，在unlockCanvas()和lockCanvas()中Surface的内容是不缓存的，所以需要完全重绘Surface的内容，为了提高效率只重绘变化的部分则可以调用lockCanvas(Rect rect)函数来指定一个rect区域，这样该区域外的内容会缓存起来。在调用lockCanvas函数获取Canvas后，SurfaceView会获取Surface的一个同步锁直到调用unlockCanvasAndPost(Canvas canvas)函数才释放该锁，这里的同步机制保证在Surface绘制过程中不会被改变（被摧毁、修改）。
#### 备注2
我没有在该surfaceview的初始化函数中将其 ScreenW 与 ScreenH 进行赋值，这里要特别注意，如果你在初始化调用ScreenW = this.getWidth();和ScreenH = this.getHeight();那么你将得到很失望的值全部为0;原因是和接口Callback接口机制有关，当我们继承callback接口会重写它的surfaceChanged()、surfaceCreated()、surfaceDestroyed(),这几个函数当surfaceCreated()被执行的时候，真正的view才被创建，也就是说之前得到的值为0 ，是因为初始化会在surfaceCreated（）方法执行以前执行，view没有的时候我们去取屏幕宽高肯定是0，所以这里要注意这一点；
#### 备注3
这里我把draw的代码都try起来，主要是为了当画的内容中一旦抛出异常了，那么我们也能在finally中执行该操作。这样当代码抛出异常的时候不会导致Surface出去不一致的状态。 
其实这就是一个简单的游戏架构了，当然还少了按键处理，声音播放等等，这些我后续会写出相关的学习文章。对于surfaceview的介绍差不多就介绍到这里了，其中的理解是看了别人的文章和自己的理解、当然可能理解的会有些偏差，但是我想不会太离谱 呵呵。