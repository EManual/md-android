代码如下：
```  
import java.io.BufferedInputStream;
import java.io.RandomAccessFile;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLConnection;
import java.util.concurrent.CountDownLatch;

import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;

public class ManyThreadActivity extends Activity {
	 /** Called when the activity is first created. */
	private Button button;
	private TextView textView;
	private static final int THREAD_COUNT = 4;
	private CountDownLatch latch = new CountDownLatch(THREAD_COUNT);
	private long completeLength = 0;
	private long fileLength;
	public static String SaveFile = Environment.getExternalStorageDirectory()
			+ "/DownFile/" + "test.Mp3";
	public static final String url = "http://210.30.12.33:8080/mp3/Beyond.mp3";

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		button = (Button) findViewById(R.id.button1);
		textView = (TextView) findViewById(R.id.editText1);
		textView.setText(url);
		button.setOnClickListener(new MyListener());
	}

	class MyListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			// TODO Auto-generated method stub
			try {
				download(url);
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}

	}

	// 进行下载的线程
	class DownloadThread extends Thread {
		private URL url;
		private RandomAccessFile file;
		private long from;
		private long end;

		 /**
		  * @param url
		  *            下载的URL
		  * @param file
		  *            下载完成之后存储的文件
		  * @param from
		  *            当前线程对应文件的起始位置
		  * @param end
		  *            当前线程对应文件的结束位置
		  */
		DownloadThread(URL url, RandomAccessFile file, long from, long end) {
			this.url = url;
			this.file = file;
			this.from = from;
			this.end = end;
		}

		public void run() {
			try {
				long pos = from;
				byte[] buf = new byte[1024 * 8];
				HttpURLConnection cn = (HttpURLConnection) url.openConnection();

				// 设置请求的起始和结束位置。
				cn.setRequestProperty("Range", "bytes=" + from + "-" + end);
				BufferedInputStream bis = new BufferedInputStream(
						cn.getInputStream());
				int len;
				while ((len = bis.read(buf)) > 0) {
					synchronized (file) {
						file.seek(pos);
						file.write(buf, 0, len);
					}
					pos += len;
					completeLength += len;
					System.out.println(completeLength * 100 / fileLength + "%");
				}
				cn.disconnect();
			} catch (Exception e) {
				e.printStackTrace();
			}
			latch.countDown();
		}
	}

	public void download(String address) throws Exception {
		URL url = new URL(address);
		URLConnection cn = url.openConnection();
		fileLength = cn.getContentLength();
		long packageLength = fileLength / THREAD_COUNT;// 每个线程要下载的字节数
		long leftLength = fileLength % THREAD_COUNT;// 剩下的字节数

		RandomAccessFile file = new RandomAccessFile(SaveFile, "rw");
		System.out.println(fileLength);
		// 计算每个线程请求的起始位置和结束位置。
		/*
		  * 第一个线程的起始位置是0~0+packageLength(每个线程要下载的字节数)
		  * 第二个线程的起始位置是endPos+1(第一个线程的packageLength+1)~endPos+1+packageLength
		  * 第二个线程的起始位置是.........
		  */
		long pos = 0;
		for (int i = 0; i < THREAD_COUNT; i++) {
			long endPos = pos + packageLength;
			new DownloadThread(url, file, pos, endPos).start();
			if (leftLength > 0) {
				endPos++;
				leftLength--;
			}
			pos = endPos;
		}
		latch.await();
	}
}
```