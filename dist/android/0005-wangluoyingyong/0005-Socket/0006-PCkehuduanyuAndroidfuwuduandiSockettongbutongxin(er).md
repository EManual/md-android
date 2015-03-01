客户端（pc端）：
```  
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.BufferedReader;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.InetAddress;
import java.net.Socket;
import java.net.UnknownHostException;
public class testPcClient {
	 /**
	  * @param args
	  * @throws InterruptedException
	  */
	public static void main(String[] args) throws InterruptedException {
		try {
			Runtime.getRuntime().exec(
					"adb shell am broadcast -a NotifyServiceStop");
			Thread.sleep(3000);
			Runtime.getRuntime().exec("adb forward tcp:12580 tcp:10086");
			Thread.sleep(3000);
			Runtime.getRuntime().exec(
					"adb shell am broadcast -a NotifyServiceStart");
			Thread.sleep(3000);
		} catch (IOException e3) {
			e3.printStackTrace();
		}
		Socket socket = null;
		try {
			InetAddress serverAddr = null;
			serverAddr = InetAddress.getByName("127.0.0.1");
			System.out.println("TCP 1111" + "C: Connecting...");
			socket = new Socket(serverAddr, 12580);
			String str = "hi,wufenglong";
			System.out.println("TCP 221122" + "C:RECEIVE");
			BufferedOutputStream out = new BufferedOutputStream(
					socket.getOutputStream());
			BufferedInputStream in = new BufferedInputStream(
					socket.getInputStream());
			BufferedReader br = new BufferedReader(new InputStreamReader(
					System.in));
			boolean flag = true;
			while (flag) {
				System.out.print("请输入1~6的数字,退出输入exit：");
				String strWord = br.readLine();// 从控制台输入1~6
				if (strWord.equals("1")) {
					out.write("1".getBytes());
					out.flush();
					System.out.println("1 finish sending the data");
					String strFormsocket = readFromSocket(in);
					System.out.println("the data sent by server is:/r/n
							+ strFormsocket);
					System.out.println("=============================================");
				} else if (strWord.equals("2")) {
					out.write("2".getBytes());
					out.flush();
					System.out.println("2 finish sending the data");
					String strFormsocket = readFromSocket(in);
					System.out.println("the data sent by server is:/r/n
							+ strFormsocket);
					System.out.println("=============================================");
				} else if (strWord.equals("3")) {
					out.write("3".getBytes());
					out.flush();
					System.out.println("3 finish sending the data");
					String strFormsocket = readFromSocket(in);
					System.out.println("the data sent by server is:/r/n
							+ strFormsocket);
					System.out.println("=============================================");
				} else if (strWord.equals("4")) {
					/* 发送命令 */
					out.write("4".getBytes());
					out.flush();
					System.out.println("send file finish sending the CMD：");
					/* 服务器反馈：准备接收 */
					String strFormsocket = readFromSocket(in);
					System.out.println("service ready receice data:UPDATE_CONTACTS:
									+ strFormsocket);
					byte[] filebytes = FileHelper.readFile("R0013340.JPG");
					System.out.println("file size=" + filebytes.length);
					/* 将整数转成4字节byte数组 */
					byte[] filelength = new byte[4];
					filelength = tools.intToByte(filebytes.length);
					/* 将.apk字符串转成4字节byte数组 */
					byte[] fileformat = null;
					fileformat = ".apk".getBytes();
					System.out.println("fileformat length=" + fileformat.length);
					/* 字节流中前4字节为文件长度，4字节文件格式，以后是文件流 */
					/* 注意如果write里的byte[]超过socket的缓存，系统自动分包写过去，所以对方要循环写完 */
					out.write(filelength);
					out.flush();
					String strok1 = readFromSocket(in);
					System.out.println("service receive filelength :" + strok1);
					// out.write(fileformat);
					// out.flush();
					// String strok2 = readFromSocket(in);
					// System.out.println("service receive fileformat :" +
					// strok2);
					System.out.println("write data to android");
					out.write(filebytes);
					out.flush();
					System.out.println("*********");
					/* 服务器反馈：接收成功 */
					String strread = readFromSocket(in);
					System.out.println(" send data success:" + strread);
					System.out.println("=============================================");
				} else if (strWord.equalsIgnoreCase("EXIT")) {
					out.write("EXIT".getBytes());
					out.flush();
					System.out.println("EXIT finish sending the data");
					String strFormsocket = readFromSocket(in);
					System.out.println("the data sent by server is:/r/n
							+ strFormsocket);
					flag = false;
					System.out.println("=============================================");
				}
			}
		} catch (UnknownHostException e1) {
			System.out.println("TCP 331133" + "ERROR:" + e1.toString());
		} catch (Exception e2) {
			System.out.println("TCP 441144" + "ERROR:" + e2.toString());
		} finally {
			try {
				if (socket != null) {
					socket.close();
					System.out.println("socket.close()");
				}
			} catch (IOException e) {
				System.out.println("TCP 5555" + "ERROR:" + e.toString());
			}
		}
	}
	/* 从InputStream流中读数据 */
	public static String readFromSocket(InputStream in) {
		int MAX_BUFFER_BYTES = 4000;
		String msg = "";
		byte[] tempbuffer = new byte[MAX_BUFFER_BYTES];
		try {
			int numReadedBytes = in.read(tempbuffer, 0, tempbuffer.length);
			msg = new String(tempbuffer, 0, numReadedBytes, "utf-8");
			tempbuffer = null;
		} catch (Exception e) {
			e.printStackTrace();
		}
		// Log.v(Service139.TAG, "msg=" + msg);
		return msg;
	}
}
```