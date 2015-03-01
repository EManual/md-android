代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.MotionEvent;
import android.view.Window;
import android.view.WindowManager;
public class MainActivity extends Activity {
	private Object object;
	private final int TIME = 50;// 备注1
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.main);
		object = new Object();
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_DOWN) {
			Log.v("Himi", "ACTION_DOWN");
		} else if (event.getAction() == MotionEvent.ACTION_UP) {
			Log.v("Himi", "ACTION_UP");
		} else if (event.getAction() == MotionEvent.ACTION_MOVE) {
			Log.v("Himi", "ACTION_MOVE");
		}
		synchronized (object) {// 备注2
			try {
				object.wait(TIME);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
		return true;// 这里一定要返回true
	}
}
```
真机与模拟器的区别：
当我们的用户在玩我们的游戏的时候，尤其是RPG这种类型的，用户肯定需要会长时间的去触屏按我们的虚拟按键，比如我们会在屏幕上画上一个虚拟方向盘类似这样子~那么其实 ACTION_MOVE 这个事件会被Android一直在响应。
为什么会一直响应 ACTION_MOVE 这个动作呢？如果用户没有移动手指而是静止不动也会一直响应？
原因有两点：
第一点是因为，Android 对于触屏事件很敏感！
第二点：虽然我们的手指感觉是静止没有移动，其实事实不是如此！当我们的手指触摸到手机屏幕上之后，感觉静止没动，其实手指在不停的微颤抖震动。不信你试试静止下手指，是不是微微动？
so~ 我们就要分析了，如果ACTION_MOVE此时间一直被Android os 一直不停的响应并处理，无疑对我们游戏的性能增加了不少的负担!
比如我们游戏线程绘图时间每次用了100ms,那么当手指触摸屏幕，这短暂的0.1秒内大概会产生10个左右的MotionEvent，并且系统会尽可能快的把这些event发给监听线程， 这样的话在这一段时间内cpu就会忙于处理onTouchEvent从而杯具点的话会造成画面一卡一卡的。
那么我们其实根本用不着按键响应这么多次，而是需要在我们每次绘图后，或者绘图前接受一次用户按键就OK了，这样能让帧率不至于下降的太厉害不是么？！so~我们要控制这个时间，让他慢下来，随着我们的绘图时间一起来合作~这样就能减不少系统线程的负担。