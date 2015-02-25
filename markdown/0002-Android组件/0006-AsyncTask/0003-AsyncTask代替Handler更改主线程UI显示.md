今天学习了一下AsyncTask这异步类的使用，也是从别人那里拿过来的，和大家分享一下，加了点注释。
```  
import android.R;
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.os.AsyncTask;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
public class AsyncTest extends Activity {
	private TextView message;
	private Button open;
	private EditText url;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.network);
		message = (TextView) findViewById(R.id.message);
		url = (EditText) findViewById(R.id.url);
		open = (Button) findViewById(R.id.open);
		open.setOnClickListener(new View.OnClickListener() {
			public void onClick(View arg0) {
				connect();
			}
		});
	}
	private void connect() {
		PageTask task = new PageTask(this);
		task.execute(url.getText().toString()); // 这个函数的参数个数是可变的，可以传0个，1个，2个或者更多，
												// 当调用这个函数系统就会自动去调用doInBackgound()函数
	}
	class PageTask extends AsyncTask<String, Integer, String> {// 这三个参数对应的是excute(String),
		// onProgressUpdate(Integer),onPostResult(string)这三个方法里面的参数类型
		// 可变长的输入参数，与AsyncTask.exucute()对应
		ProgressDialog pdialog;
		public PageTask(Context context) {
			pdialog = new ProgressDialog(context, 0);
			pdialog.setButton("cancel", new DialogInterface.OnClickListener() {
				public void onClick(DialogInterface dialog, int i) {
					dialog.cancel();
				}
			});
			pdialog.setOnCancelListener(new DialogInterface.OnCancelListener() {
				public void onCancel(DialogInterface dialog) {
					finish();
				}
			});
			pdialog.setCancelable(true);
			pdialog.setMax(100);
			pdialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
			pdialog.show();
		}
		@Override
		protected String doInBackground(String... params) { // 用来接受excute(String)里面的参数
			try {
				int i = 0;
				while (i <= 100) {
					publishProgress(i++); // 这个函数的参数的个数也是可以变化的
					Thread.sleep(1000);
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
			return "下载完成";

		}
		@Override
		protected void onCancelled() {
			super.onCancelled();
		}
		@Override
		protected void onPostExecute(String result) { // 这个参数是接受doInBackgound()的返回值的
			// 返回HTML页面的内容
			message.setText(result);
			// pdialog.dismiss();
		}
		@Override
		protected void onPreExecute() {
			// 任务启动，可以在这里显示一个对话框，这里简单处理
			message.setText("开始下载");
		}
		@Override
		protected void onProgressUpdate(Integer... values) { // 这个参数是接受publishProgress(Integer)的参数的
			// 更新进度
			System.out.println("" + values[0]);
			message.setText("" + values[0]);
			pdialog.setProgress(values[0]);
		}
	}
}
```
![img](P)  