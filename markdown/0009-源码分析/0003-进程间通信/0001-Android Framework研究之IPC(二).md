对于Android的IPC来说，除了Handler和Looper之外，还有另外一种简便的方法来实现多线程的通信，那就是AsyncTask。
AsyncTask是一个异步的方法，它允许背景运算并把结果更新到前台的UI线程之上。要实现一个AsyncTask主要有4个步骤，但并不是每一个步骤都是必需的，接下来我们就看看这四个具体的步骤：
这四个步骤是：
(1)onPreExecute() 执行背景运算前任务的初始化；
(2)doInBackground(Params...)这是AsyncTask最核心的函数，即是做背景运算；它在第一步完成之后被调用，通常在这步中还会调用方法publishProgress(Progress...)将运算结果更新到UI主线程上；
(3)onProgressUpdate(Progress...)是在publishProgress(Progress...)调用之后被执行的，需要注意到是这步执行的时间是未定的，通常在这一步中会更新相关UI；
(4)onPostExecute(Result)这一步同样是和UI相关，将运算结果Result当作参数传递给UI。
大家可能已经注意到AsyncTask除了四大步之外，还有三个重要的参数：AsyncTask<Params, Progress, Result>。三个参数为通用类型，Params是传给任务初始化的参数，Progress是做背景运算过程中和UI交互的参数，Result是背景运算传递给UI的结果。
利用好这四大步和三个参数，我们可以方便的写出一个Demo：
```  
import java.io.InputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import android.app.Activity;
import android.os.AsyncTask;
import android.os.Bundle;
import android.widget.ProgressBar;
public class myAsyncTask extends Activity {
	static ProgressBar pb;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		pb = (ProgressBar) findViewById(R.id.ProgressBar01);
		Download dl = new Download();
		dl.execute();
	}
	public class Download extends AsyncTask<Void, Integer, Void> {
		@Override
		protected Void doInBackground(Void... params) {
			int totalSize = 0;
			InputStream recevier = null;
			try {
				URL myUrl = new URL("http://www.androidchina.net/");
				URLConnection urlConn = myUrl.openConnection();
				totalSize = urlConn.getContentLength();
				recevier = urlConn.getInputStream();
				byte[] b = new byte[256];
				int length = 0;
				ength += recevier.read(b);
				while (length < totalSize) {
					length += recevier.read(b);
					publishProgress((int) (length * 100 / totalSize));
				}
				recevier.close();
			} catch (MalformedURLException e) {
				e.printStackTrace();
			} catch (Exception ex) {
				ex.printStackTrace();
			}
			return null;
		}
		protected void onProgressUpdate(Integer... progress) {
			pb.setProgress(progress[0]);
		}
	}
}
```
在这个Demo中只有第二和第三步，只有第二个参数params，是一个整型参量，把下载数据包的进度更新给UI Progressbar显示。
另外，使用AsyncTask需要注意以下几点：
1. AsyncTask的实例只能在UI线程中创建；
2. dl.execute()方法只能在UI线程中调用，并且只能调用一次，否则会抛异常。