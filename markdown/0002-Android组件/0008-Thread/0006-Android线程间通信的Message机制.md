#### 1.1.Message
代码在frameworks\base\core\java\android\Os\Message.java中。
Message.obtain函数：有多个obtain函数，主要功能一样，只是参数不一样。作用是从Message Pool中取出一个Message，如果Message Pool中已经没有Message可取则新建一个Message返回，同时用对应的参数给得到的Message对象赋值。
Message Pool：大小为10个；通过Message.mPool->（Message并且Message.next）->（Message并且Message.next）->（Message并且Message.next）...构造一个Message Pool。Message Pool的第一个元素直接new出来，然后把Message.mPool（static类的static变量）指向它。其他的元素都是使用完的Message通过Message的recycle函数清理后放到Message Pool（通过Message Pool最后一个Message的next指向需要回收的Message的方式实现）。下图为Message Pool的结构：
#### 11.2.MessageQueue
MessageQueue里面有一个收到的Message的对列：
MessageQueue.mMessages(static变量)->( Message并且Message.next)-> ( Message并且Message.next)->...，下图为接收消息的消息队列：
上层代码通过Handler的sendMessage等函数放入一个message到MessageQueue里面时最终会调用MessageQueue的enqueueMessage函数。enqueueMessage根据上面的接收的Message的队列的构造把接收到的Message放入队列中。
MessageQueue的removeMessages函数根据上面的接收的Message的队列的构造把接收到的Message从队列中删除，并且调用对应Message对象的recycle函数把不用的Message放入Message Pool中。
#### 11.3.Looper
Looper对象的创建是通过prepare函数，而且每一个Looper对象会和一个线程关联
```  
public static final void prepare() {
	if (sThreadLocal.get() != null) {
		throw new RuntimeException(
				"Only one Looper may be created per thread");
	}
	sThreadLocal.set(new Looper());
}
```
Looper对象创建时会创建一个MessageQueue，主线程默认会创建一个Looper从而有MessageQueue，其他线程默认是没有 MessageQueue的不能接收Message，如果需要接收Message则需要通过prepare函数创建一个MessageQueue。具体操作请见示例代码。
```  
private Looper() {
	mQueue = new MessageQueue();
	mRun = true;
	mThread = Thread.currentThread();
}
```
prepareMainLooper函数只给主线程调用（系统处理，程序员不用处理），它会调用prepare建立Looper对象和MessageQueue。
```  
public static final void prepareMainLooper() {
	prepare();
	setMainLooper(myLooper());
	if (Process.supportsProcesses()) {
		myLooper().mQueue.mQuitAllowed = false;
	}
}
```
Loop函数从MessageQueue中从前往后取出Message，然后通过Handler的dispatchMessage函数进行消息的处理（可见消息的处理是Handler负责的），消息处理完了以后通过Message对象的recycle函数放到Message Pool中，以便下次使用，通过Pool的处理提供了一定的内存管理从而加速消息对象的获取。至于需要定时处理的消息如何做到定时处理，请见 MessageQueue的next函数，它在取Message来进行处理时通过判断MessageQueue里面的Message是否符合时间要求来决定是否需要把Message取出来做处理，通过这种方式做到消息的定时处理。
```    
public static final void loop() {
	Looper me = myLooper();
	MessageQueue queue = me.mQueue;
	while (true) {
		Message msg = queue.next(); // might block
		// if (!me.mRun) {
		// break;
		// }
		if (msg != null) {
			if (msg.target == null) {
				// No target is a magic identifier for the quit message
				return;
			}

			if (me.mLogging != null)
				me.mLogging.println(">>>>> Dispatching to " + msg.target
						+ " " + msg.callback + ": " + msg.what);
			msg.target.dispatchMessage(msg);
			if (me.mLogging != null)
				me.mLogging.println("<<<<< Finished to" + msg.target + "
						+ msg.callback);
			msg.recycle();
		}
	}
}
```
#### 11.4.Handler
Handler的构造函数表示Handler会有成员变量指向Looper和MessageQueue，后面我们会看到没什么需要这些引用；至于callback是实现了Callback接口的对象，后面会看到这个对象的作用。
```   
public Handler(Looper looper, Callback callback) {
	mLooper = looper;
	mQueue = looper.mQueue;
	mCallback = callback;
}
public interface Callback {
	public boolean handleMessage(Message msg);
}
```
获取消息：直接通过Message的obtain方法获取一个Message对象。
```   
public final Message obtainMessage(int what, int arg1, int arg2, Object obj) {
	return Message.obtain(this, what, arg1, arg2, obj);
}
```
发送消息：通过MessageQueue的enqueueMessage把Message对象放到MessageQueue的接收消息队列中
```   
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
	boolean sent = false;
	MessageQueue queue = mQueue;
	if (queue != null) {
		msg.target = this;
		sent = queue.enqueueMessage(msg, uptimeMillis);
	} else {
		RuntimeException e = new RuntimeException(this
				+ " sendMessageAtTime() called with no mQueue");
		Log.w("Looper", e.getMessage(), e);
	}
	return sent;
}
```
线程如何处理MessageQueue中接收的消息：在Looper的loop函数中循环取出MessageQueue的接收消息队列中的消息，然后调用Hander的dispatchMessage函数对消息进行处理，至于如何处理（相应消息）则由用户指定（三个方法，优先级从高到低：Message里面的Callback，一个实现了Runnable接口的对象，其中run函数做处理工作；Handler里面的mCallback指向的一个实现了Callback接口的对象，里面的handleMessage进行处理；处理消息Handler对象对应的类继承并实现了其中handleMessage函数，通过这个实现的handleMessage函数处理消息）。
```  
public void dispatchMessage(Message msg) {
	if (msg.callback != null) {
		handleCallback(msg);
	} else {
		if (mCallback != null) {
			if (mCallback.handleMessage(msg)) {
				return;
			}
		}
		handleMessage(msg);
	}
}
```
Runnable说明：Runnable只是一个接口，实现了这个接口的类对应的对象也只是个普通的对象，并不是一个Java中的Thread。Thread类经常使用Runnable，很多人有误解，所以这里澄清一下。
其中清理Message是Looper里面的loop函数指把处理过的Message放到Message的Pool里面去，如果里面已经超过最大值10个，则丢弃这个Message对象。
调用Handler是指Looper里面的loop函数从MessageQueue的接收消息队列里面取出消息，然后根据消息指向的Handler对象调用其对应的处理方法。
#### 11.5.代码示例
1.5.1.主线程给自己发送消息示例
#### 主线程发送消息：
在onClick的case 101中创建一个继承自Handler的EventHandler对象，然后获取一个消息，然后通过EventHandler对象调用 sendMessage把消息发送到主线程的MessageQueue中。主线程由系统创建，系统会给它建立一个Looper对象和 MessageQueue，所以可以接收消息。这里只要根据主线程的Looper对象初始化EventHandler对象，就可以通过 EventHandler对象发送消息到主线程的消息队列中。
#### 主线程处理消息：
这里是通过EventHandler的handleMessage函数处理的，其中收到的Message对象中what值为一的消息就是发送给它的，然后把消息里面附带的字符串在TextView上显示出来。
1.5.2.其他线程给主线程发送消息示例
#### 其他线程发送消息（这里是说不使用Runnable作为callback的消息）：
首先 postRunnable设为false，表示不通过Runnable方式进行消息相关的操作。然后启动线程noLooerThread，然后以主线程的 Looper对象为参数建立EventHandler的对象mNoLooperThreadHandler，然后获取一个Message并把一个字符串赋值给它的一个成员obj，然后通过mNoLooperThreadHandler把消息发送到主线程的MessageQueue中。
#### 主线程处理消息：
这里是通过EventHandler的handleMessage函数处理的，其中收到的Message对象中what值为二的消息就是上面发送给它的，然后把消息里面附带的字符串在TextView上显示出来。
1.5.3.其他线程给自己发送消息示例
#### 其他线程发送消息：
其他非主线程建立后没有自己的Looper对象，所以也没有MessageQueue，需要给非主线程发送消息时需要建立MessageQueue以便接收消息。下面说明如何给自己建立MessageQueue和Looper对象。从OwnLooperThread的run函数中可以看见有一个Looper.prepare()调用，这个就是用来建立非主线程的MessageQueue和Looper对象的。
所以这里的发送消息过程是建立线程mOwnLooperThread，然后线程建立自己的Looper和MessageQueue对象，然后根据上面建立的Looper对象建立对应的EventHandler对象mOwnLooperThreadHandler，然后由 mOwnLooperThreadHandler建立消息并且发送到自己的MessageQueue里面。
#### 其他线程处理接收的消息：
线程要接收消息需要在run函数中调用Looper.loop()，然后loop函数会从MessageQueue中取出消息交给对应的Handler对象mOwnLooperThreadHandler处理，在mOwnLooperThreadHandler的handleMessage函数中会把Message对象中what值为三的消息（上面发送的消息）在Log中打印出来，可以通过Logcat工具查看
程序代码如下：
```  
import android.app.Activity;
import android.content.Context;
import android.graphics.Color;
import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.os.Message;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;
public class MessageExample extends Activity implements OnClickListener {
	private final int WC = LinearLayout.LayoutParams.WRAP_CONTENT;
	private final int FP = LinearLayout.LayoutParams.FILL_PARENT;
	public TextView tv;
	private EventHandler mHandler;
	private Handler mOtherThreadHandler = null;
	private Button btn, btn2, btn3, btn4, btn5, btn6;
	private NoLooperThread noLooerThread = null;
	private OwnLooperThread ownLooperThread = null;
	private ReceiveMessageThread receiveMessageThread = null;
	private Context context = null;
	private final String sTag = "MessageExample";
	private boolean postRunnable = false;
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		context = this.getApplicationContext();
		LinearLayout layout = new LinearLayout(this);
		layout.setOrientation(LinearLayout.VERTICAL);
		btn = new Button(this);
		btn.setId(101);
		btn.setText("message from main thread self");
		btn.setOnClickListener(this);
		LinearLayout.LayoutParams param = new LinearLayout.LayoutParams(250, 50);
		param.topMargin = 10;
		layout.addView(btn, param);
		btn2 = new Button(this);
		btn2.setId(102);
		btn2.setText("message from other thread to main thread");
		btn2.setOnClickListener(this);
		layout.addView(btn2, param);
		btn3 = new Button(this);
		btn3.setId(103);
		btn3.setText("message to other thread from itself");
		btn3.setOnClickListener(this);
		layout.addView(btn3, param);
		btn4 = new Button(this);
		btn4.setId(104);
		btn4.setText("message with Runnable as callback from other thread to main thread");
		btn4.setOnClickListener(this);
		layout.addView(btn4, param);
		btn5 = new Button(this);
		btn5.setId(105);
		btn5.setText("main thread's message to other thread");
		btn5.setOnClickListener(this);
		layout.addView(btn5, param);
		btn6 = new Button(this);
		btn6.setId(106);
		btn6.setText("exit");
		btn6.setOnClickListener(this);
		layout.addView(btn6, param);
		tv = new TextView(this);
		tv.setTextColor(Color.WHITE);
		tv.setText("");
		LinearLayout.LayoutParams param2 = new LinearLayout.LayoutParams(FP, WC);
		param2.topMargin = 10;
		layout.addView(tv, param2);
		setContentView(layout);
		// 主线程要发送消息给other thread， 这里创建那个other thread
		receiveMessageThread = new ReceiveMessageThread();
		receiveMessageThread.start();
	}
	// implement the OnClickListener interface
	@Override
	public void onClick(View v) {
		switch (v.getId()) {
		case 101:
			// 主线程发送消息给自己
			Looper looper;
			looper = Looper.myLooper(); // get the Main looper related with the
										// main thread
			// 如果不给任何参数的话会用当前线程对应的Looper(这里就是Main Looper)为Handler里面的成员mLooper赋值
			mHandler = new EventHandler(looper);
			// mHandler = new EventHandler();
			// 清除整个MessageQueue里的消息
			mHandler.removeMessages(0);
			String obj = "This main thread's message and received by itself!";
			// 得到Message对象
			Message m = mHandler.obtainMessage(1, 1, 1, obj);
			// 将Message对象送入到main thread的MessageQueue里面
			mHandler.sendMessage(m);
			break;
		case 102:
			// other线程发送消息给主线程
			postRunnable = false;
			noLooerThread = new NoLooperThread();
			noLooerThread.start();
			break;
		case 103:
			// other thread获取它自己发送的消息
			tv.setText("please look at the error level log for other thread received message");
			ownLooperThread = new OwnLooperThread();
			ownLooperThread.start();
			break;
		case 104:
			// other thread通过Post Runnable方式发送消息给主线程
			postRunnable = true;
			noLooerThread = new NoLooperThread();
			noLooerThread.start();
			break;
		case 105:
			// 主线程发送消息给other thread
			if (null != mOtherThreadHandler) {
				tv.setText("please look at the error level log for other thread received message from main thread");
				String msgObj = "message from mainThread";
				Message mainThreadMsg = mOtherThreadHandler.obtainMessage(1, 1,
						1, msgObj);
				mOtherThreadHandler.sendMessage(mainThreadMsg);
			}
			break;
		case 106:
			finish();
			break;
		}
	}
	class EventHandler extends Handler {
		public EventHandler(Looper looper) {
			super(looper);
		}
		public EventHandler() {
			super();
		}
		public void handleMessage(Message msg) {
			// 可以根据msg.what执行不同的处理，这里没有这么做
			switch (msg.what) {
			case 1:
				tv.setText((String) msg.obj);
				break;
			case 2:
				tv.setText((String) msg.obj);
				noLooerThread.stop();
				break;
			case 3:
				// 不能在非主线程的线程里面更新UI，所以这里通过Log打印收到的消息
				Log.e(sTag, (String) msg.obj);
				ownLooperThread.stop();
				break;
			default:
				// 不能在非主线程的线程里面更新UI，所以这里通过Log打印收到的消息
				Log.e(sTag, (String) msg.obj);
				break;
			}
		}
	}
	// NoLooperThread
	class NoLooperThread extends Thread {
		private EventHandler mNoLooperThreadHandler;
		public void run() {
			Looper myLooper, mainLooper;
			myLooper = Looper.myLooper();
			mainLooper = Looper.getMainLooper(); // 这是一个static函数
			String obj;
			if (myLooper == null) {
				mNoLooperThreadHandler = new EventHandler(mainLooper);
				obj = "NoLooperThread has no looper and handleMessage function executed in main thread!";
			} else {
				mNoLooperThreadHandler = new EventHandler(myLooper);
				obj = "This is from NoLooperThread self and handleMessage function executed in NoLooperThread!";
			}
			mNoLooperThreadHandler.removeMessages(0);
			if (false == postRunnable) {
				// send message to main thread
				Message m = mNoLooperThreadHandler.obtainMessage(2, 1, 1, obj);
				mNoLooperThreadHandler.sendMessage(m);
				Log.e(sTag, "NoLooperThread id:" + this.getId());
			} else {
				// 下面new出来的实现了Runnable接口的对象中run函数是在Main
				// Thread中执行，不是在NoLooperThread中执行
				// 注意Runnable是一个接口，它里面的run函数被执行时不会再新建一个线程
				// 您可以在run上加断点然后在eclipse调试中看它在哪个线程中执行
				mNoLooperThreadHandler.post(new Runnable() {
					@Override
					public void run() {
						tv.setText("update UI through handler post runnalbe mechanism!");
						noLooerThread.stop();
					}
				});
			}
		}
	}
	// OwnLooperThread has his own message queue by execute Looper.prepare();
	class OwnLooperThread extends Thread {
		private EventHandler mOwnLooperThreadHandler;
		public void run() {
			Looper.prepare();
			Looper myLooper, mainLooper;
			myLooper = Looper.myLooper();
			mainLooper = Looper.getMainLooper(); // 这是一个static函数
			String obj;
			if (myLooper == null) {
				mOwnLooperThreadHandler = new EventHandler(mainLooper);
				obj = "OwnLooperThread has no looper and handleMessage function executed in main thread!";
			} else {
				mOwnLooperThreadHandler = new EventHandler(myLooper);
				obj = "This is from OwnLooperThread self and handleMessage function executed in NoLooperThread!";
			}
			mOwnLooperThreadHandler.removeMessages(0);
			// 给自己发送消息
			Message m = mOwnLooperThreadHandler.obtainMessage(3, 1, 1, obj);
			mOwnLooperThreadHandler.sendMessage(m);
			Looper.loop();
		}
	}
	// ReceiveMessageThread has his own message queue by execute
	// Looper.prepare();
	class ReceiveMessageThread extends Thread {
		public void run() {
			Looper.prepare();
			mOtherThreadHandler = new Handler() {
				public void handleMessage(Message msg) {
					Log.e(sTag, (String) msg.obj);
				}
			};
			Looper.loop();
		}
	}
}

```
使用Looper.myLooper静态方法可以取得当前线程的Looper对象。
使用mHandler = new EevntHandler(Looper.myLooper()); 可建立用来处理当前线程的Handler对象；其中，EevntHandler是Handler的子类。
使用mHandler = new EevntHandler(Looper.getMainLooper()); 可建立用来处理main线程的Handler对象；其中，EevntHandler是Handler的子类。