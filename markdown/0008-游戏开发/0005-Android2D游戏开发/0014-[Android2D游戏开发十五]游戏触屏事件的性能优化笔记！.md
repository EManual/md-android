先上一段代码大家来看一下：
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
			Log.v("android", "ACTION_DOWN");
		} else if (event.getAction() == MotionEvent.ACTION_UP) {
			Log.v("android", "ACTION_UP");
		} else if (event.getAction() == MotionEvent.ACTION_MOVE) {
			Log.v("android", "ACTION_MOVE");
		}
		synchronized (object) {// 备注2
			try {
				object.wait(TIME);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
		return true;// 这里一定要返回true！原因请看【Android2D游戏开发之九】
	}
}
```
代码很清晰呵呵，主要就是备注2的地方：
一：前言：各位童鞋肯定都知道在模拟器中，我们的鼠标当点击一次模拟器屏幕然后释放，先触发ACTION_DOWN然后ACTION_UP；如果是在屏幕上移动那么才会触发 ACTION_MOVE 的动作；OK，很对。但是你要知道，这只是模拟器！！！！！
二：真机与模拟器的区别：   当我们的小用户（说到用户我就想起“我叫MT”中的暗夜男那句经典台词：亲爱的客户，我是嫩爹！）咳咳，回到话题；当我们的用户在玩我们的游戏的时候，尤其是RPG这种类型的，用户肯定需要会长时间的去触屏按我们的虚拟按键，比如我们会在屏幕上画上一个虚拟方向盘类似这样子~那么其实 ACTION_MOVE 这个事件会被Android一直在响应！！
三： 为什么会一直响应 ACTION_MOVE 这个动作呢？ 如果用户没有移动手指而是静止不动也会一直响应？      
原因有两点：第一点是因为，Android对于触屏事件很敏感！第二点：虽然我们的手指感觉是静止没有移动，其实事实不是如此！当我们的手指触摸到手机屏幕上之后，感觉静止没动，其实手指在不停的微颤抖震动。不信你试试静止下手指，是不是微微动？
我们就要分析了，如果ACTION_MOVE此时间一直被Android os一直不停的响应并处理，无疑对我们游戏的性能增加了不少的负担!比如我们游戏线程绘图时间每次用了100ms,那么当手指触摸屏幕，这短暂的0.1秒内大概会产生10个左右的MotionEvent,并且系统会尽可能快的把这些event发给监听线程，这样的话在这一段时间内cpu就会忙于处理onTouchEvent从而杯具点的话会造成画面一卡一卡的。 
那么我们其实根本用不着按键响应这么多次，而是需要在我们每次绘图后，或者绘图前接受一次用户按键就OK了，这样能让帧率不至于下降的太厉害不是么？！so~我们要控制这个时间，让他慢下来，随着我们的绘图时间一起来合作~这样就能减不少系统线程的负担。备注2:可能有的童鞋会问为什么不用sleep()的方法，其实如果我们只是想让线程休眠指定时间的话可以用sleep()函数，但是这个没有资源锁的限制。而Object的wait,notify方法通常用在时间不定的条件限制等待，并且必须写在同步代码块中。so~还有童鞋会问，为什么不用当前类的object来使用：this.wait(),而是new一个object来：synchronized中的Object表示Object调用wait必须拥有该对象的监视锁，当前我们有了object的锁，就要用object调用wait~
备注1：这里的变量大家都知道其实是我们设置的休眠的时间，那么这里我想拿出来跟大家说下关于这个时间的定值，在上文我也有说过我们的游戏中只要按键跟我们的绘图线程的时间一样即可，当然这里是个我们的理想时间！如果我们游戏中有人物的帧，那么我们可以来根据人物帧数来当成设定这个睡眠时间也是相当合适的，毕竟人物一帧说明逻辑执行了一遍了呵呵~这个还是根据大家游戏的情况而定吧~
注意：Object.wait(long timeout)这个方法也需要慎用！          
原因是因为测试发现：这个睡眠的时间其实比你规定的时间要略微的长一些,不过我们合理控制好时间还是没问题的。
补充1.看到有童鞋问//备注2这里能不能用this，也就是当前的object，答案是可以的，但是要注意最好不要这样用，原因是如果当其他地方也需要与当前的Object进行同步的话有可能出现死锁情况！用一个新的object的原因也就是可以让代码中该干什么就干什么，互不影响，    
2.//备注2这里其实我们可以对其优化,毕竟一个Object比较浪费，我们其实只需要一个字节就足够了，so~我们可以这样定义一个：byte[] lock = new byte[0]; 这样可以算是最优了~