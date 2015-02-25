首先明确Android之所以有Handler和AsyncTask，都是为了不阻塞主线程（UI线程），且UI的更新只能在主线程中完成，因此异步处理是不可避免的。
Android1.5提供了一个工具类：AsyncTask，它使创建需要与用户界面交互的长时间运行的任务变得更简单。不需要借助线程和Handler即可实现。
AsyncTask的优势体现在：
1、线程的开销较大，如果每个任务都要创建一个线程，那么应用程 序的效率要低很多； 
2、线程无法管理，匿名线程创建并启动后就不受程序的控制了，如果有很多个请求发送，那么就会启动非常多的线程，系统将不堪重负。 
3、另外，前面已经看到，在新线程中更新UI还必须要引入handler，这让代码看上去非常臃肿。
AsyncTask定义了三种泛型类型 Params，Progress和Result。
1、Params 启动任务执行的输入参数，比如HTTP请求的URL。 
2、Progress 后台任务执行的百分比。 
3、Result 后台执行任务最终返回的结果，比如String。
AsyncTask的执行分为四个步骤，每一步都对应一个回调方法，开发者需要实现一个或几个方法。在任务的执行过程中，这些方法被自动调用。
onPreExecute()，该方法将在执行实际的后台操作前被UI thread调用。可以在该方法中做一些准备工作，如在界面上显示一个进度条。 
doInBackground(Params...)，将在onPreExecute 方法执行后马上执行，该方法运行在后台线程中。这里将主要负责执行那些很耗时的后台计算工作。可以调用publishProgress方法来更新实时的任务进度。该方法是抽象方法，子类必须实现。 
onProgressUpdate(Progress...)，在publishProgress方法被调用后，UI thread将调用这个方法从而在界面上展示任务的进展情况，例如通过一个进度条进行展示。 
onPostExecute(Result), 在doInBackground 执行完成后，onPostExecute 方法将被UI thread调用，后台的计算结果将通过该方法传递到UI thread。
使用AsyncTask类，以下是几条必须遵守的准则： 
1) Task的实例必须在UI thread中创建。
2) execute方法必须在UI thread中调用。
3) 不要手动的调用onPreExecute(), onPostExecute(Result)，doInBackground(Params...), onProgressUpdate(Progress...)这几个方法 
4) 该task只能被执行一次，否则多次调用时将会出现异常
一个简单进度条的例子：
```  
<?xml version="1.0" encoding="UTF-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="fill_parent"
	android:layout_height="fill_parent"
	android:orientation="vertical" >
	<ProgressBar
		android:id="@+id/progress_bar"
		style="?android:attr/progressBarStyleHorizontal"
		android:layout_width="200dip"
		android:layout_height="10dip"
		android:layout_gravity="center"
		android:max="100"
		android:progress="0" >
	</ProgressBar>
</LinearLayout>
```
java代码：
```  
import android.app.Activity;
import android.os.AsyncTask;
import android.os.Bundle;
import android.widget.ProgressBar;
public class Double extends Activity {
	public ProgressBar pBar;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		pBar = (ProgressBar) findViewById(R.id.progress_bar);
		// AsyncTask.execute()要在主线程里调用
		new AsyncLoader().execute((Void) null);
	}
	public void initProgress() {
		pBar.setProgress(0);
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		pBar.setProgress(50);
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		pBar.setProgress(100);
	}
	// AsyncTask
	class AsyncLoader extends AsyncTask<Void, Void, Integer> {
		@Override
		protected Integer doInBackground(Void... params) {
			initProgress();
			return null;
		}
	}
}
```
获取网页的例子：
```  
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;
import android.os.AsyncTask;
//设置三种类型参数分别为String,Integer,String 
class PageTask extends AsyncTask<String, Integer, String> {
	// 可变长的输入参数，与AsyncTask.exucute()对应
	@Override
	protected String doInBackground(String... params) {
		try {
			HttpClient client = new DefaultHttpClient();
			// params[0] 代表连接的url
			HttpGet get = new HttpGet(params[0]);
			HttpResponse response = client.execute(get);
			HttpEntity entity = response.getEntity();
			long length = entity.getContentLength();
			InputStream is = entity.getContent();
			String s = null;
			if (is != null) {
				ByteArrayOutputStream baos = new ByteArrayOutputStream();
				byte[] buf = new byte[128];
				int ch = -1;
				int count = 0;
				while ((ch = is.read(buf)) != -1) {
					baos.write(buf, 0, ch);
					count += ch;
					if (length > 0) {
						// 如果知道响应的长度，调用publishProgress（）更新进度
						publishProgress((int) ((count / (float) length) * 100));
					}
					// 为了在模拟器中清楚地看到进度，让线程休眠100ms
					Thread.sleep(100);
				}
				s = new String(baos.toByteArray());
			}
			// 返回结果
			return s;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
	@Override
	protected void onCancelled() {
		super.onCancelled();
	}
	@Override
	protected void onPostExecute(String result) {
		// 返回HTML页面的内容
		message.setText(result);
	}
	@Override
	protected void onPreExecute() {
		// 任务启动，可以在这里显示一个对话框，这里简单处理
		message.setText(R.string.task_started);
	}
	@Override
	protected void onProgressUpdate(Integer... values) {
		// 更新进度
		message.setText(values[0]);
	}
}
```