直接看代码，注释很详细
```  
import android.app.Activity;
import android.os.AsyncTask;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.os.SystemClock;
import android.util.Log;
import android.widget.TextView;
import android.widget.Toast;
[Tags]/**
 [Tags]* 一个使用异步任务的例子。一般来说一个异步任务只执行一次，这个例子有点非主流，任务结束后会触发下一次任务执行。
 [Tags]* 由任务task在屏幕上打印数字，第一次任务执行由主Activity的onCreate触发，每次任务结束后
 [Tags]* 设定下一次触发的时间，共执行5次。对于任务来说doInBackground()接收任务的参数params，并执行产生数字的动作，每一个数字
 [Tags]* 产生后调用一次publishProgress()来更新UI，这个函数本身也是异步的只是用来发个消息调用完成后立即返回，
 [Tags]* 而产生数字的动作在继续进行。更新界面的操作在onProgressUpdate()中设定。 所有的on函数都由系统调用，不能用户调用。
 [Tags]* 代码中使用Handler是为了能触发任务执行，android规定这种异步任务每次执行完就结束，若要重新执行需要new一个新的。
 [Tags]* 异步任务只能在UI线程里面创建和执行
 [Tags]*/
public class testAsync extends Activity {
	private final int MSG_TIMER = 12;
	private TextView vText = null;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test);
		vText = (TextView) findViewById(R.id.TextView01);
		vText.setText("Num...");
		new task().execute("->");
	}
	// 接收任务task发来的消息，触发一个新的任务
	private final Handler handler = new Handler() {
		@Override
		public void handleMessage(Message msg) {
			super.handleMessage(msg);

			switch (msg.what) {
			case MSG_TIMER:
				new task().execute("->");
				break;
			}
		}

	};
	// 任务执行次数
	private static int times = 1;
	// AsyncTask<>的参数类型由用户设定，这里设为三个String
	// 第一个String代表输入到任务的参数类型，也即是doInBackground()的参数类型
	// 第二个String代表处理过程中的参数类型，也就是doInBackground()执行过程中的产出参数类型，通过publishProgress()发消息
	// 传递给onProgressUpdate()一般用来更新界面
	// 第三个String代表任务结束的产出类型，也就是doInBackground()的返回值类型，和onPostExecute()的参数类型
	private class task extends AsyncTask<String, String, String> {
		// 后台执行的耗时任务，接收参数并返回结果
		// 当onPostExecute()执行完，在后台线程中被系统调用
		@Override
		protected String doInBackground(String... params) {
			// 在这里产生数据，送给onProgressUpdate以更新界面
			String pre = params[0];
			for (int i = 0; i < 5; i++) {
				publishProgress(pre + i);
				// 这里是否需要停顿下
				SystemClock.sleep(1000);
			}
			return "任务结束";
		}
		// 任务执行结束后，在UI线程中被系统调用
		// 一般用来显示任务已经执行结束
		@Override
		protected void onPostExecute(String result) {
			super.onPostExecute(result);
			Toast.makeText(testAsync.this, result, Toast.LENGTH_SHORT).show();
			// 任务执行5次后推出
			if (times > 5) {
				return;
			}
			// 设定下一次任务触发时间
			Message msg = Message.obtain();
			msg.what = MSG_TIMER;
			handler.sendMessageDelayed(msg, 10000L);
		}
		// 最先执行，在UI线程中被系统调用
		// 一般用来在UI中产生一个进度条
		@Override
		protected void onPreExecute() {
			super.onPreExecute();
			Toast.makeText(testAsync.this, "开始执行第" + times + "次任务: " + this,
					Toast.LENGTH_SHORT).show();
			times++;
		}
		// 更新界面操作，在收到更新消息后，在UI线程中被系统调用
		@Override
		protected void onProgressUpdate(String... values) {
			super.onProgressUpdate(values);
			vText.append(values[0]);
		}
	}
}
```