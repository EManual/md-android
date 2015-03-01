Android应用程序也是消息驱动的，按道理来说也应该提供消息循环机制。实际上谷歌参考了Windows的消息循环机制，也在Android系统中实现了消息循环机制。
Android通过Looper、Handler来实现消息循环机制，Android消息循环是针对线程的(每个线程都可以有自己的消息队列和消息循环)。
本文深入介绍一下Android消息处理系统原理。
Android系统中Looper负责管理线程的消息队列和消息循环，具体实现请参考Looper的源码。 可以通过Loop.myLooper()得到当前线程的Looper对象，通过Loop.getMainLooper()可以获得当前进程的主线程的Looper对象。
前面提到Android系统的消息队列和消息循环都是针对具体线程的，一个线程可以存在(当然也可以不存在)一个消息队列和一个消息循环(Looper)，特定线程的消息只能分发给本线程，不能进行跨线程，跨进程通讯。但是创建的工作线程默认是没有消息循环和消息队列的，如果想让该线程具有消息队列和消息循环，需要在线程中首先调用Looper.prepare()来创建消息队列，然后调用Looper.loop()进入消息循环。如下例所示：
```  
class LooperThread extends Thread {
	public Handler mHandler;
	public void run() {
		Looper.prepare();
		mHandler = new Handler() {
			public void handleMessage(Message msg) {
				// process incoming messages here
			}
		};
		Looper.loop();
	}
}
```
这样你的线程就具有了消息处理机制了，在Handler中进行消息处理。
Activity是一个UI线程，运行于主线程中，Android系统在启动的时候会为Activity创建一个消息队列和消息循环(Looper)。详细实现请参考ActivityThread.java文件。
Handler的作用是把消息加入特定的(Looper)消息队列中，并分发和处理该消息队列中的消息。构造Handler的时候可以指定一个Looper对象，如果不指定则利用当前线程的Looper创建。详细实现请参考Looper的源码。
Activity、Looper、Handler的关系如下图所示：
![img](P)  
一个Activity中可以创建多个工作线程或者其他的组件，如果这些线程或者组件把他们的消息放入Activity的主线程消息队列，那么该消息就会在主线程中处理了。因为主线程一般负责界面的更新操作，并且Android系统中的weget不是线程安全的，所以这种方式可以很好的实现Android界面更新。在Android系统中这种方式有着广泛的运用。
那么另外一个线程怎样把消息放入主线程的消息队列呢?
答案是通过Handle对象，只要Handler对象以主线程的Looper创建，那么调用Handler的sendMessage等接口，将会把消息放入队列都将是放入主线程的消息队列。并且将会在Handler主线程中调用该handler的handleMessage接口来处理消息。
这里面涉及到线程同步问题，请先参考如下例子来理解Handler对象的线程模型：
1、首先创建MyHandler工程。
2、在MyHandler.java中加入如下的代码：
```  
import android.app.Activity;
import android.os.Bundle;
import android.os.Message;
import android.util.Log;
import android.os.Handler;
public class MyHandler extends Activity {
	static final String TAG = "Handler";
	Handler h = new Handler() {
		public void handleMessage(Message msg) {
			switch (msg.what) {
			case HANDLER_TEST:
				Log.d(TAG, "The handler thread id =
						+ Thread.currentThread().getId() + "\n");
				break;
			}
		}
	};
	static final int HANDLER_TEST = 1;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		Log.d(TAG, "The main thread id = " + Thread.currentThread().getId()
				+ "\n");
		new myThread().start();
		setContentView(R.layout.main);
	}
	class myThread extends Thread {
		public void run() {
			Message msg = new Message();
			msg.what = HANDLER_TEST;
			h.sendMessage(msg);
			Log.d(TAG, "The worker thread id =
					+ Thread.currentThread().getId() + "\n");
		}
	}
}
```
在这个例子中我们主要是打印，这种处理机制各个模块的所处的线程情况。
如下是我的机器运行结果：
```  
09-10 23:40:51.478: DEBUG/Handler(302): The main thread id = 1 
09-10 23:40:51.569: DEBUG/Handler(302): The worker thread id = 8 
09-10 23:40:52.128: DEBUG/Handler(302): The handler thread id = 1
```
我们可以看出消息处理是在主线程中处理的，在消息处理函数中可以安全的调用主线程中的任何资源，包括刷新界面。工作线程和主线程运行在不同的线程中，所以必须要注意这两个线程间的竞争关系。
上例中，你可能注意到在工作线程中访问了主线程handler对象，并在调用handler的对象向消息队列加入了一个消息。这个过程中会不会出现消息队列数据不一致问题呢?答案是handler对象不会出问题，因为handler对象管理的Looper对象是线程安全的，不管是加入消息到消息队列和从队列读出消息都是有同步对象保护的，具体请参考Looper.java文件。上例中没有修改handler对象，所以handler对象不可能会出现数据不一致的问题。
通过上面的分析，我们可以得出如下结论：
1、如果通过工作线程刷新界面，推荐使用handler对象来实现。
2、注意工作线程和主线程之间的竞争关系。推荐handler对象在主线程中构造完成(并且启动工作线程之后不要再修改之，否则会出现数据不一致)，然后在工作线程中可以放心的调用发送消息SendMessage等接口。
3、除了2所述的hanlder对象之外的任何主线程的成员变量如果在工作线程中调用，仔细考虑线程同步问题。如果有必要需要加入同步对象保护该变量。
4、handler对象的handleMessage接口将会在主线程中调用。在这个函数可以放心的调用主线程中任何变量和函数，进而完成更新UI的任务。
5、Android很多API也利用Handler这种线程特性，作为一种回调函数的变种，来通知调用者。这样Android框架就可以在其线程中将消息发送到调用者的线程消息队列之中，不用担心线程同步的问题。
深入理解Android消息处理机制对于应用程序开发非常重要，也可以让你对线程同步有更加深刻的认识。