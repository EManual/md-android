我们还是接上一篇的代码来讲解：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.KeyEvent;
import android.view.MotionEvent;
public class Activity01 extends Activity {
	GameSurfaceView mGameSurfaceView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 创建GameSurfaceView对象
		mGameSurfaceView = new GameSurfaceView(this);
		// 设置显示GameSurfaceView视图
		setContentView(mGameSurfaceView);
	}
	// 触笔事件 返回值为true 父视图不做处理以下返回值为true的都是不做处理的
	public boolean onTouchEvent(MotionEvent event) {
		return true;
	}
	// 按键按下事件
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		return true;
	}
	// 按键弹起事件
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		switch (keyCode) {
		// 上方向键
		case KeyEvent.KEYCODE_DPAD_UP:
			mGameSurfaceView.y -= 3;
			break;
		// 下方向键
		case KeyEvent.KEYCODE_DPAD_DOWN:
			mGameSurfaceView.y += 3;
			break;
		}
		return false;
	}
	public boolean onKeyMultiple(int keyCode, int repeatCount, KeyEvent event) {
		return true;
	}
}
```
写完这个例子 我有些疑问 
可不可以在类似这种SurfaceView上边放一些控件呢？ 
前边已经说了Surface是提供给需要直接画像素而不是使用窗体部件的应用使用的。但是我还是想知道为什么不能再Surface上边放一个控件比如一个button或者一个textview这里我们来拿button举例子，比如我们想在Surface上边放一个button，这时我们就需要new一个button按钮了呗  在Acvity01类中我们可以这样做 Button button = new Button(this); 
但是 你new 完这个 button 你怎么把它 放倒 SurfaceView 这个视图上呢？ 
因为在Activity01这个类中我们是这样设置布局的setContentView(mGameSurfaceView); 它的布局就是一个SurfaceView说白了也是一个View ，这就相当于在一个视图上在放一个视图 
这是放不了的。 
这时候有人就想了，我们可以把Button放在GameSurfaceView类中，这个想法真是聪明，太聪明了。然后我们就到 GameSurfaceView类中 这样写 Button button = new Button(this); 你会惊奇的发现他报错了。 
为什么呢？下面我把android中Button的源码贴上来大家看看
```  
@RemoteView
public class Button extends TextView {
	public Button(Context context) {
		this(context, null);
	}
	public Button(Context context, AttributeSet attrs) {
		this(context, attrs, com.android.internal.R.attr.buttonStyle);
	}
	public Button(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
}
```
Button 这个类比较简单 就三个构造方法。 但是 每个 构造方法 都需要一个 Context 对象。 
这一点大家显然都已经注意到了。 那为什么 我可以再 在继承Activity的类里边 new一个Button 而在 继承 SurfaceView的类中确不行呢。 
我们分别来看一下Activity类和GameSurfaceView类的继承关系
![img](P)  
我想大家已经知道 为什么 SurfaceView 上边不可以放一些我们常用的控件了 也知道为什么我们可以再Activity类 里边可以 new Button 而在 SurfaceView 类里边却不行 因为我们的Activity类本身就是一个 Context对象。 而我们的 SurfaceView本身不是一个Context对象。
而我们要new一个Button怎么都得需要一个Context对象。从这个角度其实就可以解释为什么我们不能再SurfaceView上边放一些我们常用的控件了 Button TextView之类的。