需求：
1.一个android端的service后台运行的程序，作为socket的服务器端;用于接收Pc client端发来的命令，来处理数据后，把结果发给PC client
2.PC端程序，作为socket的客户端，用于给android手机端发操作命令
难点分析：
1.手机一定要有adb模式，即插上USB线时马上提示的对话框选adb。好多对手机的操作都可以用adb直接作。
不过，我发现LG GW880就没有，要去下载个
2.android默认手机端的IP为“127.0.0.1”
3.要想联通PC与android手机的sokcet，一定要用adb forward 来作下端口转发才能连上socket.
```  
Runtime.getRuntime().exec("adb forward tcp:12580 tcp:10086"); 
Thread.sleep(3000);
```
4.android端的service程序Install到手机上容易，但是还要有方法来从PC的client端来启动手机上的service ，这个办法可以通过PC端adb命令来发一个Broastcast ，手机端再写个接收BroastcastReceive来接收这个Broastcast，在这个BroastcastReceive来启动service
pc端命令：
```  
Runtime.getRuntime().exec( 
"adb shell am broadcast -a NotifyServiceStart");
```
android端的代码:ServiceBroadcastReceiver.java
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;
public class ServiceBroadcastReceiver extends BroadcastReceiver {
	private static String START_ACTION = "NotifyServiceStart";
	private static String STOP_ACTION = "NotifyServiceStop";
	@Override
	public void onReceive(Context context, Intent intent) {
		Log.d(androidService.TAG, Thread.currentThread().getName() + "---->
				+ "ServiceBroadcastReceiver onReceive");
		String action = intent.getAction();
		if (START_ACTION.equalsIgnoreCase(action)) {
			context.startService(new Intent(context, androidService.class));
			Log.d(androidService.TAG, Thread.currentThread().getName()
					+ "---->" + "ServiceBroadcastReceiver onReceive start end");
		} else if (STOP_ACTION.equalsIgnoreCase(action)) {
			context.stopService(new Intent(context, androidService.class));
			Log.d(androidService.TAG, Thread.currentThread().getName()
					+ "---->" + "ServiceBroadcastReceiver onReceive stop end");
		}
	}
}
```
5.由于是USB连接，所以socket就可以设计为一但连接就一直联通，即在new socket和开完out,in流后，就用个while(true){}来循环PC端和android端的读和写
android的代码：
```  
public void run() {
	Log.d(androidService.TAG, Thread.currentThread().getName() + "---->
			+ "a client has connected to server!");
	BufferedOutputStream out;
	BufferedInputStream in;
	try {
		/* PC端发来的数据msg */
		String currCMD = "";
		out = new BufferedOutputStream(client.getOutputStream());
		in = new BufferedInputStream(client.getInputStream());
		// testSocket();// 测试socket方法
		androidService.ioThreadFlag = true;
		while (androidService.ioThreadFlag) {
			try {
				if (!client.isConnected()) {
					break;
				}
				/* 接收PC发来的数据 */
				Log.v(androidService.TAG, Thread.currentThread().getName()
						+ "---->" + "will read......");
				/* 读操作命令 */
				currCMD = readCMDFromSocket(in);
				Log.v(androidService.TAG, Thread.currentThread().getName()
						+ "---->" + "**currCMD ==== " + currCMD);
				/* 根据命令分别处理数据 */
				if (currCMD.equals("1")) {
					out.write("OK".getBytes());
					out.flush();
				} else if (currCMD.equals("2")) {
					out.write("OK".getBytes());
					out.flush();
				} else if (currCMD.equals("3")) {
					out.write("OK".getBytes());
					out.flush();
				} else if (currCMD.equals("4")) {
					/* 准备接收文件数据 */
					try {
						out.write("service receive OK".getBytes());
						out.flush();
					} catch (IOException e) {
						e.printStackTrace();
					}
					/* 接收文件数据，4字节文件长度，4字节文件格式，其后是文件数据 */
					byte[] filelength = new byte[4];
					byte[] fileformat = new byte[4];
					byte[] filebytes = null;
					/* 从socket流中读取完整文件数据 */
					filebytes = receiveFileFromSocket(in, out, filelength,
							fileformat);
					// Log.v(Service139.TAG, "receive data =" + new
					// String(filebytes));
					try {
						/* 生成文件 */
						File file = FileHelper.newFile("R0013340.JPG");
						FileHelper.writeFile(file, filebytes, 0,
								filebytes.length);
					} catch (IOException e) {
						e.printStackTrace();
					}
				} else if (currCMD.equals("exit")) {
				}
			} catch (Exception e) {
				// try {
				// out.write("error".getBytes("utf-8"));
				// out.flush();
				// } catch (IOException e1) {
				// e1.printStackTrace();
				// }
				Log.e(androidService.TAG, Thread.currentThread().getName()
						+ "---->" + "read write error111111");
			}
		}
		out.close();
		in.close();
	} catch (Exception e) {
		Log.e(androidService.TAG, Thread.currentThread().getName()
				+ "---->" + "read write error222222");
		e.printStackTrace();
	} finally {
		try {
			if (client != null) {
				Log.v(androidService.TAG, Thread.currentThread().getName()
						+ "---->" + "client.close()");
				client.close();
			}
		} catch (IOException e) {
			Log.e(androidService.TAG, Thread.currentThread().getName()
					+ "---->" + "read write error333333");
			e.printStackTrace();
		}
	}
}
```
6.如果是在PC端和android端的读写操作来while(true){}循环，这样socket流的结尾不好判断，不能用“-1”来判断，因为“-1”是只有在socket关闭时才作为判断结尾。
7.socket在out.write(bytes);时，要是数据太大时，超过socket的缓存，socket自动分包发送，所以对方就一定要用循环来多次读。最好的办法就是服务器和客户端协议好，比如发文件时，先写过来一个要发送的文件的大小，然后再发送文件;对方用这个大小，来循环读取数据。
android端接收数据的代码:
```  
 /**
  * 功能：从socket流中读取完整文件数据
  * InputStream in：socket输入流
  * byte[] filelength: 流的前4个字节存储要转送的文件的字节数
  * byte[] fileformat：流的前5-8字节存储要转送的文件的格式（如.apk）
  *  */
public static byte[] receiveFileFromSocket(InputStream in,
		OutputStream out, byte[] filelength, byte[] fileformat) {
	byte[] filebytes = null;// 文件数据
	try {
		int filelen = MyUtil.bytesToInt(filelength);// 文件长度从4字节byte[]转成Int
		String strtmp = "read file length ok:" + filelen;
		out.write(strtmp.getBytes("utf-8"));
		out.flush();
		filebytes = new byte[filelen];
		int pos = 0;
		int rcvLen = 0;
		while ((rcvLen = in.read(filebytes, pos, filelen - pos)) > 0) {
			pos += rcvLen;
		}
		Log.v(androidService.TAG, Thread.currentThread().getName()
				+ "---->" + "read file OK:file size=" + filebytes.length);
		out.write("read file ok".getBytes("utf-8"));
		out.flush();
	} catch (Exception e) {
		Log.v(androidService.TAG, Thread.currentThread().getName()
				+ "---->" + "receiveFileFromSocket error");
		e.printStackTrace();
	}
	return filebytes;
}
```
8.socket的最重要的机制就是读写采用的是阻塞的方式，如果客户端作为命令发起者，服务器端作为接收者的话，只有当客户端client用out.writer()写到输出流里后，即流中有数据service的read才会执行，不然就会一直停在read()那里等数据。
9.还要让服务器端可以同时连接多个client，即服务器端用new thread()来作数据读取操作。