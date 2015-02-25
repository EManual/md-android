目的：客户端用socket连接web服务器发送http请求，访问一个Servlet /service。并接收servlet的相应数据。
```  
public class Client {
	public static void main(String[] arg) {
		Socket socket;
		try {
			socket = new Socket("10.20.64.203", 7001);
			OutputStream os = socket.getOutputStream();
			InputStream ins = socket.getInputStream();
			String data = getXmlString();
			StringBuffer sb = new StringBuffer();
			sb.append("POST /service HTTP/1.1\r\n");// 注意\r\n为回车换行
			sb.append("Accept-Language: zh-cn\r\n");
			sb.append("Connection: Keep-Alive\r\n");
			sb.append("Host:localhost\r\n");
			sb.append("Content-Length:11\r\n");
			sb.append("\r\n");
			sb.append("data=abc\r\n");
			sb.append("\r\n");
			// 接收Web服务器返回HTTP响应包
			os.write(sb.toString().getBytes());
			os.flush();
			byte[] b = new byte[1000];
			ins.read(b); // 读取http头
			InputStreamReader ireader = new InputStreamReader(ins);
			java.io.BufferedReader reader = new java.io.BufferedReader(ireader);
			String s = reader.readLine(); // 读取内容
			System.out.println("response:" + s);
			System.out.println(reader.readLine());
			System.out.println(reader.readLine());
			System.out.println(reader.readLine());
			System.out.println(reader.readLine());
			System.out.println(reader.readLine());
			System.out.println(reader.readLine());
		} catch (UnknownHostException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```