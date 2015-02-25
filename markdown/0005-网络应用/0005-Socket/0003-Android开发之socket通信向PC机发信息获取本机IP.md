小知识点：UDP协议和TCP协议的不同。UDP是把数据都打成数据包，数据包上自带通信的地址，但是数据包发出去之后UDP协议不能保证你能否收到。而TCP协议要求接收方收到数据后给个回应，当发送重要数据的时候就可以选择TCP协议。UDP发送数据的量是有限的，而TCP是没有限制的，当然这导致UDP很快，TCP相对慢点。根据不同的情况，有不同的选择。TCP能保证数据传输的成功性，UDP只传输，不保证传输的正确性。
#### 一，通信的基本结构：客户端和服务器端 
客户端这边是Socket类：客户端指定给某个服务器端上的某个端口发送消息。比如向56.152.16.5这台机器的19001号端口发送消息。
服务器端是ServiceSocket类：服务器端就在19001号这个端口上监听，一旦有消息来立刻捕捉还可以有所回应。
客户端通过OutputStream向服务器端发送数据，服务器端通过InputStream获取数据，即OutputStream（发）——>InputStream（收） 如果服务器端想回发消息也是同样的道理。
#### 二，实例讲解编写服务器端和客户端
1，服务器端：这是个纯java的project，单独在一个工程
```  
public class ServerSocketText {
	public static void main(String[] args) {
		new ServerThread().start();
	}
}
// 创建一个线程在后台监听
class ServerThread extends Thread {
	private static int Port = 19001;
	ServerSocket serversocket = null;
	public void run() {
		try {
			// 创建一个serversocket对象，并让他在Port端口监听
			serversocket = new ServerSocket(Port);
			while (true) {
				// 调用serversocket的accept()方法，接收客户端发送的请求
				Socket socket = serversocket.accept();
				BufferedReader buffer = new BufferedReader(
						new InputStreamReader(socket.getInputStream()));
				// 读取数据
				String msg = buffer.readLine();
				System.out.println("msg:" + msg);
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				serversocket.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```
2，客户端：这是个android的客户端，用于发送消息
```  
public class Client extends Activity {
	private static String IpAddress = "56.152.16.5";
	private static int Port = 10000;
	private EditText edittext = null;
	private Button send = null;
	Socket socket = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		edittext = (EditText) findViewById(R.id.edittext);
		send = (Button) findViewById(R.id.send);
		send.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				sendMsg();
			}
		});
	}
	// 发送信息
	public void sendMsg() {
		try {
			// 创建socket对象，指定服务器端地址和端口号
			socket = new Socket(IpAddress, Port);
			// 获取 Client 端的输出流
			PrintWriter out = new PrintWriter(new BufferedWriter(
					new OutputStreamWriter(socket.getOutputStream())), true);
			// 填充信息
			out.println(edittext.getText());
			System.out.println("msg=" + edittext.getText());
			// 关闭
		} catch (UnknownHostException e1) {
			e1.printStackTrace();
		} catch (IOException e1) {
			e1.printStackTrace();
		} finally {
			try {
				socket.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```
3，注意事项：
记得在AndroidManifest.xml中添加你的网络权限
<uses-permission android:name="android.permission.INTERNET"/>
activity布局就是一个edittext和已个button，所以没贴出代码。
运行时最好先android 在服务器端，这样客户端发消息时才方便查看。
4，知识扩充：获取android手机的ip地址
只要能获取到ip地址的话，我们就可以让PC机和android实现聊天功能。但是这里我就不演示了怎么实现了。
```  
public String getLocalIpAddress() {
	try {
		for (Enumeration<NetworkInterface> en = NetworkInterface
				.getNetworkInterfaces();
		en.hasMoreElements();) {
			NetworkInterface intf = en.nextElement();
			for (Enumeration<InetAddress> enumIpAddr = intf
					.getInetAddresses();
			enumIpAddr.hasMoreElements();) {
				InetAddress inetAddress = enumIpAddr.nextElement();
				if (!inetAddress.isLoopbackAddress()) {
					return inetAddress.getHostAddress().toString();
				}
			}
		}
	} catch (SocketException ex) {
	}
	return null;
}
```