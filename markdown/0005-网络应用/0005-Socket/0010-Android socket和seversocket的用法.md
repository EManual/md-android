代码如下：
```  
public class Server {
	public static void main(String[] args) {
		Socket socket = null;
		BufferedReader br = null;
		PrintWriter pw = null;
		try {
			// 创建服务器,并开放3081端口
			ServerSocket server = new ServerSocket(3081);
			while (true) {
				// 监听服务器端口，一旦有数据发送过来，那么就将数据封装成socket对象
				// 如果没有数据发送过来，那么这时处于线程阻塞状态，不会向下继续执行
				socket = server.accept();
				System.out.println("客户端信息：" + socket.getRemoteSocketAddress());
				// 从socket中得到读取流，该流中有客户端发送过来的数据
				InputStream in = socket.getInputStream();
				// InputStreamReader将字节流转化为字符流
				br = new BufferedReader(new InputStreamReader(in));
				// 行读取客户端数据
				String info = br.readLine();
				System.out.println(info);
				OutputStream out = socket.getOutputStream();
				pw = new PrintWriter(out);
				pw.println("服务器说：我扁死你");
				pw.flush();
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				pw.close();
				br.close();
				socket.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```