说到Android的IPC(Inter-Process Conmmunication)首先想到的就是Handler和Looper，Handler用于多进程之间的通信和数据交换，将各进程之间通信的数据Message放置到Message Queue里，而Looper用于创建各进程自身的message queue，然后在适当的时候分发给相应的进程。
我们知道在Android中，每一个UI线程是一个主线程(Main Thread)，Android为每一个主线程维护一个Message Queue，当用户需要长时间的背景线程操作的时候，需要create自己的new thread，这样的new thread是没有自己的message queue的，只能共享主线程的message queue并且将所做的运算结果和数据通过Handler发送到主线程的message queue里，被主线程共享。
Xml代码:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ProgressBar
        android:id="@+id/ProgressBar01"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="150dip"
        android:layout_height="wrap_content" >
    </ProgressBar>
</LinearLayout>
```
这个xml文件创建了一个progressbar，并且将style设置成水平style="?android:attr/progressBarStyleHorizontal)
Xml代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="waterlife.ipc.demo"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".IPCConmunication"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```
为了访问网络，需要在manifest file里设置access internet的permission
<uses-permission android:name="android.permission.INTERNET" />)
Java代码 ：
```  
import java.io.InputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.HandlerThread;
import android.os.Looper;
import android.os.Message;
import android.widget.ProgressBar;
public class IPCConmunication extends Activity {
	static ProgressBar pb;
	final int UPDATE_PROGRESS_BAR = 1000;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		pb = (ProgressBar) findViewById(R.id.ProgressBar01);
		Download dl = new Download();
		new Thread(dl).start();
	}
	Handler mHandle = new Handler() {
		public void handleMessage(Message msg) {
			switch (msg.what) {
			case UPDATE_PROGRESS_BAR:
				pb.setProgress(msg.arg1);
				break;
			default:
				break;
			}
		}
	};
	class Download implements Runnable {
		@Override
		public void run() {
			int totalSize = 0;
			InputStream recevier = null;
			try {
				URL myUrl = new URL("http://bbs.nju.edu.cn");
				URLConnection urlConn = myUrl.openConnection();
				totalSize = urlConn.getContentLength();
				recevier = urlConn.getInputStream();
				byte[] b = new byte[256];
				int length = 0;
				length += recevier.read(b);
				while (length < totalSize) {
					Message msg = mHandle.obtainMessage(UPDATE_PROGRESS_BAR);
					msg.arg1 = (int) (length * 100 / totalSize);
					if (mHandle.hasMessages(UPDATE_PROGRESS_BAR)) {
						mHandle.removeMessages(UPDATE_PROGRESS_BAR);
					}
					mHandle.sendMessage(msg);
					length += recevier.read(b);
					Thread.sleep(1000); // 睡眠1S，这个方法是不值得推荐的，因为它会使线程独占CPU，在以后的例子会使用更加有效的方法
				}
				recevier.close();
			} catch (MalformedURLException e) {
				e.printStackTrace();
			} catch (Exception ex) {
				ex.printStackTrace();
			}
		}
	}
} 	
```
我们create出来的一个new thread，用这个线程去网络上下载数据包，并且把下载的进度更新到UI主线程的progressbar上。两个线程之间的通信是用Handler来传递的。在这里新的线程Download和UI main thread共用message queue。
当然，我们可以为自己新建的线程设置自身的message queue，方法如下：
Java代码:
```  
import java.io.InputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.HandlerThread;
import android.os.Looper;
import android.os.Message;
import android.widget.ProgressBar;
public class IPCConmunication extends Activity {
	static ProgressBar pb;
	final int UPDATE_PROGRESS_BAR = 1000;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		pb = (ProgressBar) findViewById(R.id.ProgressBar01);
		Download dl = new Download();
		new Thread(dl).start();
	}
	class Download implements Runnable {
		@Override
		public void run() {
			int totalSize = 0;
			InputStream recevier = null;
			HandlerThread threadLoop = new HandlerThread("Download");
			threadLoop.start();
			Looper mLooper = threadLoop.getLooper();
			Handler mHandle = new Handler(mLooper) {
				public void handleMessage(Message msg) {
					switch (msg.what) {
					case UPDATE_PROGRESS_BAR:
						pb.setProgress(msg.arg1);
						break;
					default:
						break;
					}
				}
			};
			try {
				URL myUrl = new URL("http://bbs.nju.edu.cn");
				URLConnection urlConn = myUrl.openConnection();
				totalSize = urlConn.getContentLength();
				recevier = urlConn.getInputStream();
				byte[] b = new byte[256];
				int length = 0;
				length += recevier.read(b);
				while (length < totalSize) {
					Message msg = mHandle.obtainMessage(UPDATE_PROGRESS_BAR);
					msg.arg1 = (int) (length * 100 / totalSize);
					if (mHandle.hasMessages(UPDATE_PROGRESS_BAR)) {
						mHandle.removeMessages(UPDATE_PROGRESS_BAR);
					}
					mHandle.sendMessage(msg);
					length += recevier.read(b);
					Thread.sleep(1000);
				}
				recevier.close();
			} catch (MalformedURLException e) {
				e.printStackTrace();
			} catch (Exception ex) {
				ex.printStackTrace();
			}
		}
	}
}
```
HandlerThread是一个专门用于新建Looper的线程类，它实现了Looper.prepare()和Looper.loop()的方法。HandlerThread ceate一个新的Looper并且绑定到新线程的Handler上，实现了对新线程创建自己的Message queue的目的。