client：代码如下：
```  
public class client extends Activity {
	Button cx;
	String line;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		cx = (Button) findViewById(R.id.cx);
		try {
			Socket socket = new Socket("连接到服务器上的IP地址", 30000);
			// 将Socket对应的输入流包装
			BufferedReader br = new BufferedReader(new InputStreamReader(
					socket.getInputStream()));
			line = br.readLine();
			br.close();
			socket.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
		cx.setOnClickListener(new MyClickListener());
	}
	// 输出来自服务器的消息
	class MyClickListener implements View.OnClickListener {
		public void onClick(View args) {
			Toast.makeText(client.this, line, Toast.LENGTH_LONG).show();

		}
	}
}
```
Server（仅测试）:
用hibernate连接到数据库，进行数据的查询，然后返回给客户端 
```  
public class simpleServer {
	public static void main(String[] args) throws IOException {
		Login stu = new Login(); // 实例化JavaBean
		Session se = null;
		se = connUitl.getSession();
		stu = (Login) se.get(Login.class, 1);
		// 创建一个ServerSocket，用于监听客户端Socket的连接请求
		ServerSocket ss = new ServerSocket(30000);
		// 采用循环，不断接收来自客户端的请求
		while (true) {
			// 每当接收到客户端Socket请求，服务器端也对应产生一个Socket
			Socket s = ss.accept();
			if (stu == null) {
				System.out.println("无此信息");
				OutputStream os = s.getOutputStream();
				os.write("无此信息".getBytes("utf-8"));
				os.close();
				s.close();
			} else {
				System.out.println("信息如下：");
				int id = stu.getId();
				String name = stu.getName();
				byte[] n = name.getBytes("utf-8");
				String password = stu.getPassword();
				byte[] p = password.getBytes("utf-8");
				OutputStream os_id = s.getOutputStream();
				OutputStream os_n = s.getOutputStream();
				OutputStream os_p = s.getOutputStream();
				os_id.write("1".getBytes("utf-8"));
				os_n.write(n);
				os_p.write(p);
				os_id.close();
				os_n.close();
				os_p.close();
				s.close();
			}
			se.close();
		}
	}
}
```
数据库连接工具：
```  
public class ConnUitl {
	private static SessionFactory sf;
	static {
		Configuration cf = new Configuration().configure();
		sf = cf.buildSessionFactory();
	}
	public static Session getSession() {
		return sf.openSession();
	}
}
```
使用socket进行android网络编程，连接到数据库，很不方便，一般会采用HttpClient来实现android客户端编程