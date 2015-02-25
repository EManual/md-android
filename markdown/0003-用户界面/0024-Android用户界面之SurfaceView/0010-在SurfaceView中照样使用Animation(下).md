下面我们来看MySurfaceView.java中的代码：
```   
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.util.AttributeSet;
import android.view.KeyEvent;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.SurfaceHolder.Callback;
public class MySurfaceView extends SurfaceView implements Callback, Runnable {
	public static MySurfaceView msrv;// ----备注1
	private int move_x = 2, x = 20;
	private Thread th;
	private SurfaceHolder sfh;
	private Canvas canvas;
	private Paint p;
	public MySurfaceView(Context context, AttributeSet attrs) {
		super(context, attrs);
		msrv = this;
		p = new Paint();
		p.setAntiAlias(true);
		sfh = this.getHolder();
		sfh.addCallback(this);
		th = new Thread(this);
		this.setKeepScreenOn(true);
		this.setFocusable(true);// ----备注2
	}
	public void surfaceCreated(SurfaceHolder holder) {
		th.start();
	}
	public void draw() {
		canvas = sfh.lockCanvas();
		if (canvas != null) {
			canvas.drawColor(Color.WHITE);
			canvas.drawText("我是   - Surfaceview", x + move_x, 280, p);
			sfh.unlockCanvasAndPost(canvas);
		}
	}
	private void logic() {
		x += move_x;
		if (x > 200 || x < 80) {
			move_x = -move_x;
		}
	}
	@Override
	public boolean onKeyDown(int key, KeyEvent event) { // 备注2
		return super.onKeyDown(key, event);
	}
	public void run() {
		while (true) {
			draw();
			logic();
			try {
				Thread.sleep(100);
			} catch (Exception ex) {
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
代码都很熟悉了， 主要我们来给大家解释下备注1,备注2:
备注1：
我在两个MyView 和 MySurfaceView中都定义了本类一个静态对象，然后在初始化的时候都利用=this的形式进行了实例化;
注意:=this; 的这种实例形式要注意!只能在当前程序中仅存在一个本类对象才可使用!
为什么要实例两个View的实例而且定义成静态,这样做主要为了类之间方便调用和操作！比如在我们这个项目中，我这样做是为了在MainActivity中去管理两个View按键焦点！下面我会给出MainActivity的代码，大家一看便知；
备注2：   
我在两个MyView和MySurfaceView中都对获取按键焦点注释掉了，而是在别的类中的调用其View的静态实例对象就可以任意类中对其设置！这样就可以很容易去控制到底谁来响应按键了。
这里还要强调一下：当xml中注册多个 View的时候，当我们点击按键之后，Android会先判定哪个View setFocusable(true)设置焦点了，如果都设置了，那么Android会默认响应在xml中第一个注册的view，而不是两个都会响应。那么为什么不同时响应呢？我解释下：
```  
java.lang.Object
	|__android.view.View
		|__android.view.SurfaceView
```
上面这是Android SDK Api的树状图，很明显SurfaceView继承了View，它俩是基继承关系，那么不管是子类还是基类一旦响应了按键，其基类或者父类就不会再去响应；下面我们来看MainActivity.java：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.KeyEvent;
import android.view.Window;
import android.view.WindowManager;
public class MainActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		setContentView(R.layout.main);
		MySurfaceView.msrv.setFocusable(false);// 备注1
		MyView.mv.setFocusable(true);// 备注1
	}
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {// 备注2
		return super.onKeyDown(keyCode, event);
	}
}
```
#### 备注1：   
这里是当程序运行的时候我们默认让我们的MyView(View)来响应按键。通过类名调用对应的View实例，然后设置获取焦点的函数；
#### 备注2：    
这里要注意:不管你在xml中注册了多少个View，也不管View是否都设置了获取焦点，只要你在MainActivity中重写onKeyDown()函数，Android就会调用此函数。那么直接在SurfaceView中进行实现动画的想法这里没有得到很好的解决，而是我利用布局的方式来一同显示的方式，希望各位童鞋如果有好的方法，在SurfaceView中直接能使用动画的建议和想法，希望留言给我，大家一起学习讨论。