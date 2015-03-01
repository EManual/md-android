注意问题：为了实现对于多个客户端的处理，使用了多线程的操作，每个线程维护一个Socket的连接与通信，新连接的Socket的管道被加入到ArrayList中。对于输出流的操作是对于所有的连接的客户端进行写数据。对于关闭了Socket的客户端管道从List中移除。
客户端编程代码：
```  
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.Socket;
import java.net.SocketAddress;
import java.net.UnknownHostException;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
public class SocketActivity extends Activity {
	EditText editText = null;
	Button sendButton = null;
	TextView display = null;
	Socket client = null;
	MyHandler myHandler;
	DataOutputStream dout;
	DataInputStream din;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.clientsocket);
		editText = (EditText) findViewById(R.id.message);
		sendButton = (Button) findViewById(R.id.send);
		display = (TextView) findViewById(R.id.display);
		sendButton.setOnClickListener(listener);
		try {
			client = new Socket("192.168.0.120", 50003);
			dout = new DataOutputStream(client.getOutputStream());
			din = new DataInputStream(client.getInputStream());
		} catch (UnknownHostException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
		myHandler = new MyHandler();
		MyThread m = new MyThread();
		m.start();
	}
	class MyHandler extends Handler {
		public MyHandler() {
		}
		// 子类必须重写此方法,接受数据
		@Override
		public void handleMessage(Message msg) {
			// TODO Auto-generated method stub
			Log.d("MyHandler", "handleMessage......");
			super.handleMessage(msg);
			// 此处可以更新UI
			if (client != null && client.isConnected()) {
				Log.i("handler..", "*-----*");
				try {
					dout.writeUTF("connect...");
					String message = din.readUTF();
					if (!message.equals(""))
						display.setText(display.getText().toString() + "
								+ "服务器发来的消息--：" + message);
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
	}
	class MyThread extends Thread {
		public void run() {
			while (true) {
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				Message msg = new Message();
				SocketActivity.this.myHandler.sendMessage(msg);
			}
		}
	}
	OnClickListener listener = new OnClickListener() {
		@Override
		public void onClick(View v) {
			String sendText = editText.getText().toString();
			try {
				// din = new DataInputStream(client.getInputStream());
				dout.writeUTF(sendText);
				/*
				  * display.setText(display.getText().toString() + " " +
				  * "服务器发来的消息：" + din.readUTF());
				  */
				/*
				  * display.setText(display.getText().toString() + " " +
				  * "服务器发来的消息--：" + din.readUTF());
				  */
			} catch (UnknownHostException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	};
}
```