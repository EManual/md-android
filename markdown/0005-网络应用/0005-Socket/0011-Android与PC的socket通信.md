我们今天就来说说android怎么样才能和PC通信，这个我们就要用到socket了，那么我们现在就来用一个小例子来看看。在代码中我都用了注释。
```  
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.ServerSocket;
import java.net.Socket;
public class YaoChatServer extends Thread {
	private YaoChatServer() throws IOException {
		CreateSocket();
		// 创建Socket服务器
	}
	public void run() {
		Socket client;
		String txt;
		try {
			while (true)
			// 线程无限循环，实时监听socket端口
			{
				client = ResponseSocket();
				// 响应客户端链接请求。。
				while (true) {
					txt = ReceiveMsg(client);
					System.out.println(txt);
					// 链接获得客户端发来消息，并将其显示在Server端的屏幕上
					SendMsg(client, txt);
					// 向客户端返回消息
					if (true)
						break;
					// 中断，继续等待链接请求
				}
				CloseSocket(client);
				// 关闭此次链接
			}
		} catch (IOException e) {
			System.out.println(e);
		}
	}
	private ServerSocket server = null;
	private static final int PORT = 5000;
	private BufferedWriter writer;
	private BufferedReader reader;
	private void CreateSocket() throws IOException {
		server = new ServerSocket(PORT, 100);
		System.out.println("Server starting..");
	}
	private Socket ResponseSocket() throws IOException {
		Socket client = server.accept();
		System.out.println("client connected..");
		return client;
	}
	private void CloseSocket(Socket socket) throws IOException {
		reader.close();
		writer.close();
		socket.close();
		System.out.println("client closed..");
	}
	private void SendMsg(Socket socket, String Msg) throws IOException {
		writer = new BufferedWriter(new OutputStreamWriter(
				socket.getOutputStream()));
		writer.write(Msg + "\n");
		writer.flush();
	}
	private String ReceiveMsg(Socket socket) throws IOException {
		reader = new BufferedReader(new InputStreamReader(
				socket.getInputStream()));
		System.out.println("server get input from client socket..");
		String txt = "Sever send:" + reader.readLine();
		return txt;
	}
	public static void main(final String args[]) throws IOException {
		YaoChatServer yaochatserver = new YaoChatServer();
		if (yaochatserver != null) {
			yaochatserver.start();
		}
	}
}
```
代码如下：
```  
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.net.UnknownHostException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.Socket;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.*;
public class YaoChatRoomAndroid extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		findviews();
		setonclick();
	}
	private EditText chattxt;
	private TextView chattxt2;
	private Button chatok;
	public void findviews() {
		chattxt = (EditText) this.findViewById(R.id.chattxt);
		chattxt2 = (TextView) this.findViewById(R.id.chattxt2);
		chatok = (Button) this.findViewById(R.id.chatOk);
	}
	private void setonclick() {
		chatok.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				try {
					connecttoserver(chattxt.getText().toString());
				} catch (UnknownHostException e) {
					e.printStackTrace();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		});
	}
	public void connecttoserver(String socketData) throws UnknownHostException,
			IOException {
		Socket socket = RequestSocket("192.168.10.119", 5000);
		SendMsg(socket, socketData);
		String txt = ReceiveMsg(socket);
		this.chattxt2.setText(txt);
	}
	private Socket RequestSocket(String host, int port)
			throws UnknownHostException, IOException {
		Socket socket = new Socket(host, port);
		return socket;
	}
	private void SendMsg(Socket socket, String msg) throws IOException {
		BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(
				socket.getOutputStream()));
		writer.write(msg.replace("\n", " ") + "\n");
		writer.flush();
	}
	private String ReceiveMsg(Socket socket) throws IOException {
		BufferedReader reader = new BufferedReader(new InputStreamReader(
				socket.getInputStream()));
		String txt = reader.readLine();
		return txt;
	}
}
```
配置文件如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/chattxt2"
        android:layout_width="319px"
        android:layout_height="68px"
        android:layout_alignParentLeft="true"
        android:layout_alignParentTop="true"
        android:text="TextView" >
    </TextView>
    <EditText
        android:id="@+id/chattxt"
        android:layout_width="319px"
        android:layout_height="52px"
        android:layout_alignParentLeft="true"
        android:layout_below="@+id/widget30"
        android:text="EditText"
        android:textSize="18sp" >
    </EditText>
    <Button
        android:id="@+id/chatOk"
        android:layout_width="320px"
        android:layout_height="41px"
        android:layout_alignParentLeft="true"
        android:layout_below="@+id/widget29"
        android:text="Button" >
    </Button>
</LinearLayout>
```