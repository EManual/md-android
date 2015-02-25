当我们需要开发一个复杂的游戏的时候，而且对程序的执行效率要求很高时，View类就不能满足需求了，这时必须用SurfaceView类进行开发。例如，对速度要求很高的游戏时，View类就不能满足需求了，这时必须使用SurfaceView类进行开发。例如，对速度要求很高的游戏，可以使用双缓冲来显示。游戏中的背景、人物、动画等都需要绘制在一个画布(Canvas)上，而SurfaceView可以直接访问一个画布，SurfaceView是提供给需要直接画像素而不是使用窗体部件的应用使用的。 每个Surface创建一个Canvas对象(但属性时常改变),用来管理View和Surface上的绘图操作。 
在使用SurfaceView开发时需要注意的是，使用它绘图时，一般都是出现在最顶层。使用时还需要对其进行创建、销毁、情况改变时进行监视，这就要实现SurfaceHolder.Callback接口，如果要对被绘制的画布进行裁剪，控制其大小都需要使用SurfaceHolder来完成处理。在程序中，SurfaceHolder对象需要通过getHolder方法来获得，同时还需要addCallback方法来添加 "回调函数"。
在一般的情况下，应用程序的View都是在相同的GUI线程中绘制的。这个主应用程序线程同时也用来处理所有的用户交互(例如，按钮单击或者文本输入)。 
当需要快速地更新View的UI，或者当渲染代码阻塞GUI线程的时间过长的时候，SurfaceView就是解决上述问题的最佳选择。 
SurfaceView封装了一个Surface对象，而不是Canvas。这一点很重要，因为Surface可以使用后台线程绘制。对于那些资源敏感的操作，或者那些要求快速更新或者高速帧率的地方，例如，使用3D图形，创建游戏，或者实时预览摄像头，这一点特别有用。 
#### 1.何时应该使用SurfaceView？ 
SurfaceView使用的方式与任何View所派生的类都是完全相同的。可以像其他View那样应用动画，并把它们放到布局中。 
SurfaceView封装的Surface支持使用本章前面所描述的所有标准Canvas方法进行绘图，同时也支持完全的OpenGL ES库。 
使用OpenGL，你可以再Surface上绘制任何支持的2D或者3D对象，与在2D画布上模拟相同的效果相比，这种方法可以依靠硬件加速(可用的时候)来极大地提高性能。 
对于显示动态的3D图像来说，例如，那些使用Google Earth功能的应用程序，或者那些提供沉浸体验的交互式游戏，SurfaceView特别有用。它还是实时显示摄像头预览的最佳选择。 
#### 2.创建一个新的SurfaceView控件 
要创建一个新的SurfaceView，需要创建一个新的扩展了SurfaceView的类，并实现SurfaceHolder.Callback。 
SurfaceHolder回调可以在底层的Surface被创建和销毁的时候通知View，并传递给它对SurfaceHolder对象的引用，其中包含了当前有效的Surface。 
一个典型的Surface View设计模型包括一个由Thread所派生的类，它可以接收对当前的SurfaceHolder的引用，并独立地更新它。 
#### 3.SurfaceHolder.Callback回调接口的方法介绍 
```  
surfaceChanged: 在Surface的大小发生改变时激发。 
surfaceCreated: 在创建Surface时激发。
surfaceDestroyed: 在销毁Surface时激发。
addCallback: 给SurfaceView添加一个回调函数。
lockCanvas: 锁定画布，绘图之前必须锁定画布才能得到当前画布对象。 
unlockCanvasAndPost: 开始绘图时锁定了画布，绘制完成之后解锁画布。 
removeCallback: 从SurfaceView 中移除回调函数。
```
SurfaceView和View的明显不同之处在于，继承SurfaceView的视图可以另起一个线程或者说在子线程中更新视图这与我上一篇的另外一个示例android自定义View类的简单使用示例（这个示例请点这里http://www.eoeandroid.com/thread-72273-1-1.html）明显的一个区别就是SurfaceView的画图方法 是在 子线程中执行的 而 View类的那个示例 的画图方法 是在UI线程中执行的。 
其实SurfaceView按道理应该违背了android的单线程模型 因为它在子线称中 画图 刷新界面 来更新UI 至于 它为什么可以这么做 我也不知道 有时间研究一下吧  可能它是一种特殊的视图吧。 
还有要注意的一点就是我们在绘图之前必须使用lockCanvas方法锁定画布，并得到画布，然后再画布上绘制；当绘制完成后，使用unlockCanvasAndPost 方法解锁画布，然后就显示到屏幕上。SurfaceView 类的事件处理规则和View一样。 
下面是这样一个例子，这个例子实现了一个不断变换颜色的圆形并且实现了SurfaceView的事件处理。我们可以通过模拟器的上下键来调节这个圆在屏幕中的位置。下面先看一下运行效果吧。
效果图：
![img](P)  
![img](P)  
![img](P)  
```  
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
public class GameSurfaceView extends SurfaceView implements
		SurfaceHolder.Callback, Runnable {
	// 控制循环
	boolean mbLoop = false;
	// 定义SurfaceHolder对象
	SurfaceHolder mSurfaceHolder = null;
	int miCount = 0;
	int y = 50;
	public GameSurfaceView(Context context) {
		super(context);
		// 实例化SurfaceHolder
		mSurfaceHolder = this.getHolder();
		// 添加回调 函数
		// 注意这里这句 mSurfaceHolder.addCallback(this)这句执行完了之后
		// 马上就会回调 surfaceCreated方法了 然后开启线程 执行绘图方法这里这个执行顺序要搞清楚
		mSurfaceHolder.addCallback(this);
		this.setFocusable(true);
		mbLoop = true;
	}
	// 在surface的大小发生改变时激发
	@Override
	public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
	}
	// surface创建时激发 此方法在主线程总执行
	@Override
	public void surfaceCreated(SurfaceHolder holder) {
		// 开启绘图线程
		new Thread(this).start();
	}
	// 在surface销毁时激发
	@Override
	public void surfaceDestroyed(SurfaceHolder holder) {
		// 停止循环
		mbLoop = false;
	}
	// 绘图循环
	@Override
	public void run() {
		while (mbLoop) {
			try {
				Thread.sleep(200);
			} catch (Exception e) {
			}
			// 至于这里为什么同步这就像一块画布 你不能让两个人同时往上边画画
			synchronized (mSurfaceHolder) {
				Draw();
			}
		}
	}
	// 绘图方法 注意这里是另起一个线程来执行绘图方法了不是在UI 线程了
	public void Draw() {
		// 锁定画布，得到canvas 用SurfaceHolder对象的lockCanvas方法
		Canvas canvas = mSurfaceHolder.lockCanvas();
		if (mSurfaceHolder == null || canvas == null) {
			return;
		}
		if (miCount < 100) {
			miCount++;
		} else {
			miCount = 0;
		}
		// 绘图
		Paint mPaint = new Paint();
		// 给Paint对象加上抗锯齿标志
		// http://tech.techweb.com.cn/thread-459611-1-1.html 详细说明可以看看这里
		// --->大家直接复制链接到 浏览器打开吧。
		mPaint.setAntiAlias(true);
		mPaint.setColor(Color.BLACK);
		// 绘制矩形---清屏作用
		canvas.drawRect(0, 0, 320, 480, mPaint);
		switch (miCount % 4) {
		case 0:
			mPaint.setColor(Color.BLUE);
			break;
		case 1:
			mPaint.setColor(Color.GREEN);
			break;
		case 2:
			mPaint.setColor(Color.RED);
		case 3:
			mPaint.setColor(Color.YELLOW);
		default:
			mPaint.setColor(Color.WHITE);
			break;
		}
		// 绘制矩形
		canvas.drawCircle((320 - 25) / 2, y, 50, mPaint);
		// 绘制后解锁，绘制后必须解锁才能显示
		mSurfaceHolder.unlockCanvasAndPost(canvas);
	}
}
```