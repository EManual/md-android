Socket 编程基础知识：
主要分服务器端编程和客户端编程。
#### 服务器端编程步骤：
1：创建服务器端套接字并绑定到一个端口上(0-1023是系统预留的，最好大约1024)。
2：套接字设置监听模式等待连接请求。
3：接受连接请求后进行通信。
4：返回，等待赢一个连接请求。
#### 客户端编程步骤：
1：创建客户端套接字(指定服务器端IP地址与端口号)。
2：连接(Android 创建Socket时会自动连接)。
3：与服务器端进行通信。
4：关闭套接字。
#### Android Socket通信原理注意：
1：中间的管道连接是通过InputStream/OutputStream流实现的。
2：一旦管道建立起来可进行通信。
3：关闭管道的同时意味着关闭Socket。
4：当对同一个Socket创建重复管道时会异常。
5：通信过程中顺序很重要：服务器端首先得到输入流，然后将输入流信息输出到其各个客户端。
客户端先建立连接后先写入输出流，然后再获得输入流。不然活有EOFException的异常。
```  
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
public class Server {
	static ServerSocket aServerSocket = null; // Server Socet.
	DataInputStream aDataInput = null; // Server input Stream that to
	// receive msg from client.
	DataOutputStream aDataOutput = null; // Server output Stream that to
	static ArrayList list = new ArrayList();
	public static void main(String[] args) {
		try {
			aServerSocket = new ServerSocket(50003); // listen 8888 port.
			System.out.println("already listen 50003 port.");
		} catch (Exception e) {
			e.printStackTrace();
		}
		int num = 0;
		while (num < 10) {
			Socket aSessionSoket = null;
			try {
				aSessionSoket = aServerSocket.accept();
				MyThread thread = new Server().new MyThread(aSessionSoket);
				thread.start();
				num = list.size();
			} catch (IOException e1) {
				e1.printStackTrace();
			}
		}
	}
	class MyThread extends Thread {
		Socket aSessionSoket = null;
		public MyThread(Socket socket) {
			aSessionSoket = socket;
		}
		public void run() {
			try {
				aDataInput = new DataInputStream(aSessionSoket.getInputStream());
				aDataOutput = new DataOutputStream(
						aSessionSoket.getOutputStream());
				list.add(aDataOutput);
				while (true) {
					String msg = aDataInput.readUTF(); // read msg.
					if (!msg.equals("connect...")) {
						System.out.println("ip: 
								+ aSessionSoket.getInetAddress());// ip.System.out.println("receive msg:
																	// + msg);
						for (int i = 0; i < list.size(); i++) {
							DataOutputStream output = (DataOutputStream) list
									.get(i);
							output.writeUTF(msg + "----" + list.size());
						}
						if (msg.equals("end"))
							break;
					}
					aDataOutput.writeUTF("");
				}
			} catch (IOException e) {
				e.printStackTrace();
			} finally {
				try {
					aDataInput.close();
					if (aDataOutput != null)
						aDataOutput.close();
					list.remove(aDataOutput);
					aSessionSoket.close();
				} catch (Exception e2) {
				}
			}
		}
	}
}
```