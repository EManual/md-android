在这里先向各位童鞋道个歉!我解释下：当我在给大家讲解的时候会附带上源码，可是这个源码是演示代码，为了让大家看的清楚，所以我会尽可能把一些与其无关的删掉，但是发现演示代码还是被一些童鞋们效仿，导致不少童鞋问我为什么程序执行后切入后台重新进入会报异常的问题！(这里我就全面讲解下运行机制，希望以后大家有类似问题自己就能解决了哈~)
切入后台操作比如点击HOME按键，点击返回按键...
那么重新进入程序报异常主要Surfaceiew 有两点会报异常：
第一：提交画布异常！如下图(模拟器错误提示，以及Logcat Detail)
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
		Log.v("android", "draw is Error!");
	} finally {//备注1  
		if (canvas != null)//备注2  
			sfh.unlockCanvasAndPost(canvas);
	}  
}
```
先看备注1这里，之前的文章中我给大家解释过为什么要把 sfh.unlockCanvasAndPost(canvas); 写在finally中，主要是为了保证能正常的提交画布.今天主要说说备注2，这里一定要判定下canvas是否为空，因为当程序切入后台的时候，canvas是获取不到的！那么canvas一旦为空，提交画布这里就会出现参数异常的错误！下面来说另外一种情况：线程启动异常!如下图(模拟器错误提示，以及Logcat Detail)
![img](P)  
这种异常只是在当你程序运行期间点击Home按钮后再次进入程序的时候报的异常,异常说咱们的线程已经启动！为什么返回按钮就没事？OK，下面我们就要来先详细讲解一下Android中Back和Home按键的机制！然后分析问题，并且解决问题！先看下面MySurfaceViewAnimation.java的类中的代码:
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
 [Tags]* @author android
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
				R.drawable.android_dream);
		sfh = this.getHolder();
		sfh.addCallback(this);
		paint = new Paint();
		paint.setAntiAlias(true);
		this.setLongClickable(true);
		th = new Thread(this, "android_Thread_one");
		Log.e("android", "surfaceChanged");
	}
	public void surfaceCreated(SurfaceHolder holder) {
		th.start();
		Log.e("android", "surfaceCreated");
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		Log.e("android", "surfaceChanged");
	}
	public void surfaceDestroyed(SurfaceHolder holder) {
		Log.e("android", "surfaceDestroyed");
	}
	public void draw() {
		try {
			canvas = sfh.lockCanvas();
			if (canvas != null) {
				canvas.drawColor(Color.WHITE);
				canvas.drawBitmap(bmp, bmp_x, bmp_y, paint);
			}
		} catch (Exception e) {
			Log.v("android", "draw is Error!");
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
以上是我们常用的自定义SurfaceView，并且使用Runnable接口老框架了不多说了,其中我在本类的构造、创建、状态改变、消亡函数都加上打印！OK，下面看第一张图:(刚运行程序)
![img](P)  
上图的左边部分是Dubug!这里显示我们有一条线程在运行，名字叫"android_Thread_one";上图的左边部分是LogCat日志!大家很清晰的看到，当第一次进入程序的时候，会先进入view构造函数、然后是创建view、然后是view状态改变、OK，这个大家都知道!下面我来点击Home(手机上的小房子)按键！这时程序处于后台！然后重新进入程序的过程！
![img](P)  
上图可以看出我们的线程还是一条、这里主要观察从点击home到再次进入程序的过程：(过程如下):点击home调用了view销毁、然后进入程序会先进入view创建,最后是view状态改变!上面的过程很容易理解，重要的角色上场了~Back 按钮！点我点击Back按钮看看发生了什么！
![img](P)  
先看左边的Debug一栏，多了一条线程! 看LogCat发现比点击Home按键多调用了一次构造函数！好了，从我们测试的程序来看，无疑，点击Home 和 点击 Back按钮再次进入程序的时候，步骤是不一样的,线程数量也变了！     那么这里就能解释为什么我们点击Back按钮不异常、点击Home会异常了！     原因：因为点击Back按钮再次进入程序的时候先进入的是view构造函数里，那么就是说这里又new了一个线程出来，并启动！那么而我们点击Home却不一样了,因为点击home之后再次进入程序不会进入构造函数，而是直接进入了view创建这个函数，而在view创建这个函数中我们有个启动线程的操作，其实第一次启动程序的线程还在运行，so~这里就一定异常了，说线程已经启动！                 
有些童鞋会问，我们为何不把th = new Thread(this, "android_Thread_one");放在view创建函数中不就好了？？！！          
没错，可以！但是当你反复几次之后你发现你的程序中会多出很多条进程！（如下图）
![img](P)  
虽然可以避免出现线程已经启动的异常，很明显这不是我们想要的结果！那么下面给大家介绍最合适的解决方案：修改MySurfaceViewAnimation.java
```    
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.util.Log;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.SurfaceHolder.Callback;
[Tags]/**
 [Tags]* @author android
 [Tags]* @SurfaceView 运行机制详解(修改后的最佳方式)
 [Tags]*/
public class MySurfaceViewAnimation extends SurfaceView implements Callback,
		Runnable {
	private Thread th;
	private SurfaceHolder sfh;
	private Canvas canvas;
	private Paint paint;
	private Bitmap bmp;
	private int bmp_x, bmp_y;
	private boolean android; // 备注1
	public MySurfaceViewAnimation(Context context) {
		super(context);
		this.setKeepScreenOn(true);
		bmp = BitmapFactory.decodeResource(getResources(),
				R.drawable.android_dream);
		sfh = this.getHolder();
		sfh.addCallback(this);
		paint = new Paint();
		paint.setAntiAlias(true);
		this.setLongClickable(true);
		Log.e("android", "surfaceChanged");
	}
	public void surfaceCreated(SurfaceHolder holder) {
		android = true;
		th = new Thread(this, "android_Thread_one");// 备注2
		th.start();
		Log.e("android", "surfaceCreated");
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		Log.e("android", "surfaceChanged");
	}
	public void surfaceDestroyed(SurfaceHolder holder) {
		android = false;// 备注3
		Log.e("android", "surfaceDestroyed");
	}
	public void draw() {
		try {
			canvas = sfh.lockCanvas();
			if (canvas != null) {
				canvas.drawColor(Color.WHITE);
				canvas.drawBitmap(bmp, bmp_x, bmp_y, paint);
			}
		} catch (Exception e) {
			Log.v("android", "draw is Error!");
		} finally {
			if (canvas != null)
				sfh.unlockCanvasAndPost(canvas);
		}
	}
	public void run() {
		while (android) {// 备注4
			draw();
			try {
				Thread.sleep(100);
			} catch (Exception ex) {
			}
		}
	}
}
```
这里修改的地方有以下几点:       
1.我们都知道一个线程启动后，只要run方法执行结束，线程就销毁了，所以我增加了一个布尔值的成员变量android（备注1），这里可以控制我们的线程消亡的一个开关！（备注4）       
2.在启动线程之前，设置这个布尔值为ture，让线程一直运行.       
3.在view销毁时，设置这个布尔值为false，销毁当前线程！（备注3）       
4.把我们创建线程实例也放在view创建中！(备注2),为什么要放这里，因为不管点击Back还是Home都会执行view创建函数的！如果你写在构造里，那么点击home的时候就又空指针啦！     
OK,这里图和解释够详细了，希望大家以后真正开发一款游戏的时候，一定要严谨代码，不要留有后患哈~