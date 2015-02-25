#### 1. 单线程模型
当一个程序第一次启动时，Android会同时启动一个对应的主线程（Main Thread），主线程主要负责处理与UI相关的事件，如用户的按键事件，用户接触屏幕的事件以及屏幕绘图事件，并把相关的事件分发到对应的组件进行处理。所以主线程通常又被叫做UI线程。
在开发Android应用时必须遵守单线程模型的原则：Android UI操作并不是线程安全的并且这些操作必须在UI线程中执行。
#### 1.1 子线程更新UI
Android的UI是单线程(Single-threaded)的。为了避免拖住GUI，一些较费时的对象应该交给独立的线程去执行。如果幕后的线程来执行UI对象，Android就会发出错误讯息CalledFromWrongThreadException。以后遇到这样的异常抛出时就要知道怎么回事了！
#### 1.2 Message Queue
在单线程模型下，为了解决类似的问题，Android设计了一个Message Queue(消息队列)， 线程间可以通过该Message Queue并结合Handler和Looper组件进行信息交换。下面将对它们进行分别介绍：
#### 1. Message
Message消息，理解为线程间交流的信息，处理数据后台线程需要更新UI，则发送Message内含一些数据给UI线程。
#### 2. Handler
Handler处理者，是Message的主要处理者，负责Message的发送，Message内容的执行处理。后台线程就是通过传进来的Handler对象引用来sendMessage(Message)。而使用Handler，需要implement该类的handleMessage(Message)方法，它是处理这些Message的操作内容，例如Update UI。通常需要子类化Handler来实现handleMessage方法。
#### 3. Message Queue
Message Queue消息队列，用来存放通过Handler发布的消息，按照先进先出执行。
每个message queue都会有一个对应的Handler。Handler会向message queue通过两种方法发送消息：sendMessage或post。这两种消息都会插在message queue队尾并按先进先出执行。但通过这两种方法发送的消息执行的方式略有不同：通过sendMessage发送的是一个message对象,会被Handler的handleMessage()函数处理；而通过post方法发送的是一个runnable对象，则会自己执行。
#### 4. Looper
Looper是每条线程里的Message Queue的管家。Android没有Global的Message Queue，而Android会自动替主线程(UI线程)建立Message Queue，但在子线程里并没有建立Message Queue。所以调用Looper.getMainLooper()得到的主线程的Looper不为NULL，但调用Looper.myLooper()得到当前线程的Looper就有可能为NULL。
对于子线程使用Looper，API Doc提供了正确的使用方法：
```  
class LooperThread extends Thread {
	public Handler mHandler;
	public void run() {
		Looper.prepare(); // 创建本线程的Looper并创建一个MessageQueue
		mHandler = new Handler() {
			public void handleMessage(Message msg) {
				// process incoming messages here
			}
		};
		Looper.loop(); // 开始运行Looper,监听Message Queue
	}
}
```
这个Message机制的大概流程：
1. 在Looper.loop()方法运行开始后，循环地按照接收顺序取出Message Queue里面的非NULL的Message。
2. 一开始Message Queue里面的Message都是NULL的。当Handler.sendMessage(Message)到Message Queue，该函数里面设置了那个Message对象的target属性是当前的Handler对象。随后Looper取出了那个Message，则调用该Message的target指向的Hander的dispatchMessage函数对Message进行处理。
在dispatchMessage方法里，如何处理Message则由用户指定，三个判断，优先级从高到低：
1) Message里面的Callback，一个实现了Runnable接口的对象，其中run函数做处理工作；
2) Handler里面的mCallback指向的一个实现了Callback接口的对象，由其handleMessage进行处理；
3) 处理消息Handler对象对应的类继承并实现了其中handleMessage函数，通过这个实现的handleMessage函数处理消息。
由此可见，我们实现的handleMessage方法是优先级最低的！
3. Handler处理完该Message (update UI) 后，Looper则设置该Message为NULL，以便回收！
在网上有很多文章讲述主线程和其他子线程如何交互，传送信息，最终谁来执行处理信息之类的，个人理解是最简单的方法——判断Handler对象里面的Looper对象是属于哪条线程的，则由该线程来执行！
1. 当Handler对象的构造函数的参数为空，则为当前所在线程的Looper；
2. Looper.getMainLooper()得到的是主线程的Looper对象，Looper.myLooper()得到的是当前线程的Looper对象。
1.3 AsyncTask版：
```  
import java.util.ArrayList;
import android.R;
import android.app.Dialog;
import android.app.ListActivity;
import android.app.ProgressDialog;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
public class ListProgressDemo extends ListActivity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.listprogress);
		((Button) findViewById(R.id.load_Handler))
				.setOnClickListener(new View.OnClickListener() {
					public void onClick(View view) {
						data = null;
						data = new ArrayList<String>();
						adapter = null;
						showDialog(PROGRESS_DIALOG);
						new ProgressThread(handler, data).start();
					}
				});
	}
	@Override
	protected Dialog onCreateDialog(int id) {
		switch (id) {
		case PROGRESS_DIALOG:
			return ProgressDialog.show(this, "", "Loading. Please wait...",
					true);
		default:
			return null;
		}
	}
}
private class ProgressThread extends Thread {
	private Handler handler;
	private ArrayList<String> data;
	public ProgressThread(Handler handler, ArrayList<String> data) {
		this.handler = handler;
		this.data = data;
	}
	@Override
	public void run() {
		for (int i = 0; i < 8; i++) {
			data.add("ListItem"); // 后台数据处理
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				Message msg = handler.obtainMessage();
				Bundle b = new Bundle();
				b.putInt("state", STATE_ERROR);
				msg.setData(b);
				handler.sendMessage(msg);
			}
			Message msg = handler.obtainMessage();
			Bundle b = new Bundle();
			b.putInt("state", STATE_FINISH);
			msg.setData(b);
			handler.sendMessage(msg);
		}
	} // 此处甚至可以不需要设置Looper，因为Handler默认就使用当前线程的Looper
	private final Handler handler = new Handler(Looper.getMainLooper()) {
		public void handleMessage(Message msg) { // 处理Message，更新ListView
			int state = msg.getData().getInt("state");
			switch (state) {
			case STATE_FINISH:
				dismissDialog(PROGRESS_DIALOG);
				Toast.makeText(getApplicationContext(), "加载完成!",
						Toast.LENGTH_LONG).show();
				adapter = new ArrayAdapter<String>(getApplicationContext(),
						android.R.layout.simple_list_item_1, data);
				setListAdapter(adapter);
				break;
			case STATE_ERROR:
				dismissDialog(PROGRESS_DIALOG);
				Toast.makeText(getApplicationContext(), "处理过程发生错误!",
						Toast.LENGTH_LONG).show();
				adapter = new ArrayAdapter<String>(getApplicationContext(),
						android.R.layout.simple_list_item_1, data);
				setListAdapter(adapter);
				break;
			default:
			}
		}
	};
	private ArrayAdapter<String> adapter;
	private ArrayList<String> data;
	private static final int PROGRESS_DIALOG = 1;
	private static final int STATE_FINISH = 1;
	private static final int STATE_ERROR = -1;
}
```
Android另外提供了一个工具类：AsyncTask。它使得UI thread的使用变得异常简单。它使创建需要与用户界面交互的长时间运行的任务变得更简单，不需要借助线程和Handler即可实现。
1) 子类化AsyncTask。
2) 实现AsyncTask中定义的下面一个或几个方法。
```  
onPreExecute() 开始执行前的准备工作；
doInBackground(Params...) 开始执行后台处理，可以调用publishProgress方法来更新实时的任务进度；
onProgressUpdate(Progress...)  在publishProgress方法被调用后，UI thread将调用这个方法从而在界面上展示任务的进展情况，例如通过一个进度条进行展示。
onPostExecute(Result) 执行完成后的操作，传送结果给UI线程。
```
这4个方法都不能手动调用。而且除了doInBackground(Params...)方法，其余3个方法都是被UI线程所调用的，所以要求：
1) AsyncTask的实例必须在UI thread中创建；
2) AsyncTask.execute方法必须在UI thread中调用；
同时要注意：该task只能被执行一次，否则多次调用时将会出现异常。而且是不能手动停止的，这一点要注意，看是否符合你的需求！
在使用过程中，发现AsyncTask的构造函数的参数设置需要看明白：AsyncTask<Params, Progress, Result>
Params对应doInBackground(Params...)的参数类型。而new AsyncTask().execute(Params... params)，就是传进来的Params数据，你可以execute(data)来传送一个数据，或者execute(data1, data2, data3)这样多个数据。
Progress对应onProgressUpdate(Progress...)的参数类型；
Result对应onPostExecute(Result)的参数类型。
当以上的参数类型都不需要指明某个时，则使用Void，注意不是void。不明白的可以参考上面的例子，或者API Doc里面的例子。