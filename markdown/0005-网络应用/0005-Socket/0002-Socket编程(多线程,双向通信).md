#### 一、概述
网络通信无论在手机还是其他设备上都应用得非常广泛，因此掌握网络编程是非常有必要的，而我觉得socket编程是网络编程的基础。在进入正题之前，先介绍几点网络知识，
一：socket编程有分TCP和UDP两种，TCP是基于连接的，而UDP是无连接的；
二：一个TCP连接包括了输入和输出两条独立的路径；
三：服务器必须先运行然后客户端才能与它进行通信。
四：客户端与服务器所使用的编码方式要相同，否则会出现乱码。
加入了多线程，这样UI线程就不会被阻塞；实现了客户端和服务器的双向通信，只要客户端发起了连接并成功连接后那么两者就可以随意进行通信了。
#### 二、实现
新建MyClient工程，修改main.xml文件，如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <EditText
        android:id="@+id/edittext"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:hint="请输入要发送的内容" />
    <Button
        android:id="@+id/connectbutton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="连接" />
    <Button
        android:id="@+id/sendbutton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="发送" />
</LinearLayout>
```
接着，修改MyclientActivity.java文件，主要是定义了一个继承自Thread类的用于接收数据的类，覆写了其中的run（）方法，在这个函数里面接收数据，接收到数据后就通过Handler发送消息，收到消息后在UI线程里更新接收到的数据。完整的内容如下：
```  
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.UnsupportedEncodingException;
import java.net.Socket;
import java.net.UnknownHostException;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;
public class MyClientActivity extends Activity {
	private EditText mEditText = null;
	private Button connectButton = null;
	private Button sendButton = null;
	private TextView mTextView = null;
	private Socket clientSocket = null;
	private OutputStream outStream = null;
	private Handler mHandler = null;
	private ReceiveThread mReceiveThread = null;
	private boolean stop = true;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mEditText = (EditText) this.findViewById(R.id.edittext);
		mTextView = (TextView) this.findViewById(R.id.retextview);
		connectButton = (Button) this.findViewById(R.id.connectbutton);
		sendButton = (Button) this.findViewById(R.id.sendbutton);
		sendButton.setEnabled(false);
		// 连接按钮监听
		connectButton.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				try {
					// 实例化对象并连接到服务器
					clientSocket = new Socket("113.114.170.246", 8888);
				} catch (UnknownHostException e) {
					e.printStackTrace();
				} catch (IOException e) {
					e.printStackTrace();
				}
				displayToast("连接成功！");
				// 连接按钮使能
				connectButton.setEnabled(false);
				// 发送按钮使能
				sendButton.setEnabled(true);
				mReceiveThread = new ReceiveThread(clientSocket);
				stop = false;
				// 开启线程
				mReceiveThread.start();
			}
		});
		// 发送数据按钮监听
		sendButton.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				byte[] msgBuffer = null;
				// 获得EditTex的内容
				String text = mEditText.getText().toString();
				try {
					// 字符编码转换
					msgBuffer = text.getBytes("GB2312");
				} catch (UnsupportedEncodingException e1) {
					e1.printStackTrace();
				}
				try {
					// 获得Socket的输出流
					outStream = clientSocket.getOutputStream();
				} catch (IOException e) {
					e.printStackTrace();
				}
				try {
					// 发送数据
					outStream.write(msgBuffer);
				} catch (IOException e) {
					e.printStackTrace();
				}
				// 清空内容
				mEditText.setText("");
				displayToast("发送成功！");
			}
		});
		// 消息处理
		mHandler = new Handler() {
			@Override
			public void handleMessage(Message msg) {
				// 显示接收到的内容
				mTextView.setText((msg.obj).toString());
			}
		};
	}
	// 显示Toast函数
	private void displayToast(String s) {
		Toast.makeText(this, s, Toast.LENGTH_SHORT).show();
	}
	private class ReceiveThread extends Thread {
		private InputStream inStream = null;
		private byte[] buf;
		private String str = null;
		ReceiveThread(Socket s) {
			try {
				// 获得输入流
				this.inStream = s.getInputStream();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		@Override
		public void run() {
			while (!stop) {
				this.buf = new byte[512];
				try {
					// 读取输入数据（阻塞）
					this.inStream.read(this.buf);
				} catch (IOException e) {
					e.printStackTrace();
				}
				// 字符编码转换
				try {
					this.str = new String(this.buf, "GB2312").trim();
				} catch (UnsupportedEncodingException e) {
					e.printStackTrace();
				}
				Message msg = new Message();
				msg.obj = this.str;
				// 发送消息
				mHandler.sendMessage(msg);
			}
		}
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		if (mReceiveThread != null) {
			stop = true;
			mReceiveThread.interrupt();
		}
	}
}
```
同样，新建MyServer工程，修改里面的main.xml文件，在里面添加了两个TextView，一个用于显示客户端的IP，一个用于显示接收到的内容，一个用于发送数据的Button，还有一个EditText，完整的main.xml如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/iptextview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textSize="20dip" />
    <EditText
        android:id="@+id/sedittext"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:hint="请输入要发送的内容" />
    <Button
        android:id="@+id/sendbutton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="发送" />
    <TextView
        android:id="@+id/textview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:textSize="15dip" />
</LinearLayout>
```
接着，修改MyServerActivity.java文件，定义了两个Thread子类，一个用于监听客户端的连接，一个用于接收数据，其他地方与MyClientActivity.java差不多。完整的内容如下：
```  
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.UnsupportedEncodingException;
import java.net.ServerSocket;
import java.net.Socket;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;
public class MyServerActivity extends Activity {
	private TextView ipTextView = null;
	private EditText mEditText = null;
	private Button sendButton = null;
	private TextView mTextView = null;
	private OutputStream outStream = null;
	private Socket clientSocket = null;
	private ServerSocket mServerSocket = null;
	private Handler mHandler = null;
	private AcceptThread mAcceptThread = null;
	private ReceiveThread mReceiveThread = null;
	private boolean stop = true;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		ipTextView = (TextView) this.findViewById(R.id.iptextview);
		mEditText = (EditText) this.findViewById(R.id.sedittext);
		sendButton = (Button) this.findViewById(R.id.sendbutton);
		sendButton.setEnabled(false);
		mTextView = (TextView) this.findViewById(R.id.textview);
		// 发送数据按钮监听
		sendButton.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				byte[] msgBuffer = null;
				// 获得EditTex的内容
				String text = mEditText.getText().toString();
				try {
					// 字符编码转换
					msgBuffer = text.getBytes("GB2312");
				} catch (UnsupportedEncodingException e1) {
					e1.printStackTrace();
				}
				try {
					// 获得Socket的输出流
					outStream = clientSocket.getOutputStream();
				} catch (IOException e) {
					e.printStackTrace();
				}
				try {
					// 发送数据
					outStream.write(msgBuffer);
				} catch (IOException e) {
					e.printStackTrace();
				}
				// 清空内容
				mEditText.setText("");
				displayToast("发送成功！");
			}
		});
		// 消息处理
		mHandler = new Handler() {
			@Override
			public void handleMessage(Message msg) {
				switch (msg.what) {
				case 0: {
					// 显示客户端IP
					ipTextView.setText((msg.obj).toString());
					// 使能发送按钮
					sendButton.setEnabled(true);
					break;
				}
				case 1: {
					// 显示接收到的数据
					mTextView.setText((msg.obj).toString());
					break;
				}
				}
			}
		};
		mAcceptThread = new AcceptThread();
		// 开启监听线程
		mAcceptThread.start();
	}
	// 显示Toast函数
	private void displayToast(String s) {
		Toast.makeText(this, s, Toast.LENGTH_SHORT).show();
	}
	private class AcceptThread extends Thread {
		@Override
		public void run() {
			try {
				// 实例化ServerSocket对象并设置端口号为8888
				mServerSocket = new ServerSocket(8888);
			} catch (IOException e) {
				e.printStackTrace();
			}
			try {
				// 等待客户端的连接（阻塞）
				clientSocket = mServerSocket.accept();
			} catch (IOException e) {
				e.printStackTrace();
			}
			mReceiveThread = new ReceiveThread(clientSocket);
			stop = false;
			// 开启接收线程
			mReceiveThread.start();
			Message msg = new Message();
			msg.what = 0;
			// 获取客户端IP
			msg.obj = clientSocket.getInetAddress().getHostAddress();
			// 发送消息
			mHandler.sendMessage(msg);
		}
	}
	private class ReceiveThread extends Thread {
		private InputStream mInputStream = null;
		private byte[] buf;
		private String str = null;
		ReceiveThread(Socket s) {
			try {
				// 获得输入流
				this.mInputStream = s.getInputStream();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		@Override
		public void run() {
			while (!stop) {
				this.buf = new byte[512];
				// 读取输入的数据(阻塞读)
				try {
					this.mInputStream.read(buf);
				} catch (IOException e1) {
					e1.printStackTrace();
				}
				// 字符编码转换
				try {
					this.str = new String(this.buf, "GB2312").trim();
				} catch (UnsupportedEncodingException e) {
					e.printStackTrace();
				}
				Message msg = new Message();
				msg.what = 1;
				msg.obj = this.str;
				// 发送消息
				mHandler.sendMessage(msg);
			}
		}
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		if (mReceiveThread != null) {
			stop = true;
			mReceiveThread.interrupt();
		}
	}
}
```
两个工程都修改好了，在模拟器上运行客户端：
![img](P)  
在真机上运行服务器端：
![img](P)  
接着，点击客户端的“连接”按钮，看到“连接成功”提示后输入一些内容再点击“发送”按钮，此时客户端显示：
![img](P)  
服务器端显示：
![img](P)  
接下来两边都可以随意发送数据了。