在Android的客户端编程中（特别是SNS类型的客户端），经常需要实现注册功能Activity，要用户输入用户名，密码，邮箱，照片后注册。但这时就有一个问题，在HTML中用form表单就能实现如上的注册表单，需要的信息会自动封装为完整的HTTP协议，但在Android中如何把这些参数和需要上传的文件封装为HTTP协议呢？
我们可以先做个试验，看一下form表单到底封装了什么样的信息。
第一步：编写一个Servlet，把接收到的HTTP信息保存在一个文件中，代码如下：
```  
public void doPost(HttpServletRequest request, HttpServletResponse response)
		throws ServletException, IOException {
	// 获取输入流，是HTTP协议中的实体内容
	ServletInputStream sis = request.getInputStream();
	// 缓冲区
	byte buffer[] = new byte[1024];
	FileOutputStream fos = new FileOutputStream("d:\\file.log");
	int len = sis.read(buffer, 0, 1024);
	// 把流里的信息循环读入到file.log文件中
	while (len != -1) {
		fos.write(buffer, 0, len);
		len = sis.readLine(buffer, 0, 1024);
	}
	fos.close();
	sis.close();
}
```
第二步：实现如下一个表单页面， 详细的代码如下：
```  
<form action="servlet/ReceiveFile" method="post" enctype="multipart/form-data> 
	第一个参数<input type="text" name="name1"/><br/>
	第二个参数<input type="text" name="name2"/><br/>
	第一个上传的文件<input type="file" name="file1"/><br/>
	第二个上传的文件<input type="file" name="file2"/> <br/>
	<input type="submit" value="提交">
</form>
```
从表单源码可知，表单上传的数据有4个：参数name1和name2，文件file1和file2
首先从file.log观察两个参数name1和name2的情况。这时候使用UltraEdit打开file.log（因为有些字符在记事本里显示不出来，所以要用16进制编辑器）
结合16进制数据和记事本显示的数据可知上传参数部分的格式规律：
1. 第一行是“—————————–7d92221b604bc”作为分隔符，然后是“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
2. 第二行
（1）首先是HTTP中的扩展头部分“Content-Disposition: form-data;”，表示上传的是表单数据。
（2）“name=”name1″”参数的名称。
（3）“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
3.第三行：“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
4.第四行：参数的值，最后是“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
由观察可得，表单上传的每个参数都是按照以上1—4的格式构造HTTP协议中的参数部分。
结合16进制数据和记事本显示的数据可知上传文件部分的格式规律：
1.第一行是“—————————–7d92221b604bc”作为分隔符，然后是“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
2.第二行：
a)首先是HTTP中的扩展头部分“Content-Disposition: form-data;”，表示上传的是表单数据。
b)“name=”file2″;”参数的名称。
c)“filename=”C:\2.txt””参数的值。
d)“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
3.第三行：HTTP中的实体头部分“Content-Type: text/plain”：表示所接收到得实体内容的文件格式。计算机的应用中有多种多种通用的文件格式，人们为每种通用格式都定义了一个名称，称为MIME，MIME的英文全称是”Multipurpose Internet Mail Extensions” （多功能Internet 邮件扩充服务）
4.第四行：“\r\n”（即16进制编辑器显示的0D 0A）回车换行符。
5.第五行开始：上传的内容的二进制数。
6.最后是结束标志“—————————–7d92221b604bc–”，注意：这个结束标志和分隔符的区别是最后多了“–”部分。
但现在还有一个问题，就是分隔符“—————————–7d92221b604bc”是怎么确定的呢？是不是一定要“7d92221b604bc”这串数字?
我们以前的分析只是观察了HTTP请求的实体部分，可以借用工具观察完整的HTTP请求看一看有没有什么线索？
在IE下用HttpWatch，在Firefox下用Httpfox这个插件，可以实现网页数据的抓包，从图4可看出，原来在Content-Type部分指定了分隔符所用的字符串。
根据以上总结的注册表单中的参数传递和文件上传的规律，我们可以能写出Android中实现一个用户注册功能（包括个人信息填写和上传图片部分）的工具类，
首先，要有一个javaBean类FormFile封装文件的信息：
```  
public class FormFile {
	/* 上传文件的数据 */
	private byte[] data;
	/* 文件名称 */
	private String filname;
	/* 表单字段名称 */
	private String formname;
	/* 内容类型 */
	private String contentType = "application/octet-stream"; // 需要查阅相关的资料
	public FormFile(String filname, byte[] data, String formname,
			String contentType) {
		this.data = data;
		this.filname = filname;
		this.formname = formname;
		if (contentType != null)
			this.contentType = contentType;
	}
	public byte[] getData() {
		return data;
	}
	public void setData(byte[] data) {
		this.data = data;
	}
	public String getFilname() {
		return filname;
	}
	public void setFilname(String filname) {
		this.filname = filname;
	}
	public String getFormname() {
		return formname;
	}
	public void setFormname(String formname) {
		this.formname = formname;
	}
	public String getContentType() {
		return contentType;
	}
	public void setContentType(String contentType) {
		this.contentType = contentType;
	}
}
```
实现文件上传的代码如下：
```  
 /**
  * 直接通过HTTP协议提交数据到服务器,实现表单提交功能
  * @param actionUrl
  *            上传路径
  * @param params
  *            请求参数 key为参数名,value为参数值
  * @param file
  *            上传文件
  */
public static String post(String actionUrl, Map<String, String> params,
		FormFile[] files) {
	try {
		String BOUNDARY = "———7d4a6d158c9"; // 数据分隔线
		String MULTIPART_FORM_DATA = "multipart/form-data";
		URL url = new URL(actionUrl);
		HttpURLConnection conn = (HttpURLConnection) url.openConnection();
		conn.setDoInput(true);// 允许输入
		conn.setDoOutput(true);// 允许输出
		conn.setUseCaches(false);// 不使用Cache
		conn.setRequestMethod("POST");
		conn.setRequestProperty("Connection", "Keep-Alive");
		conn.setRequestProperty("Charset", "UTF-8");
		conn.setRequestProperty("Content-Type", MULTIPART_FORM_DATA
				+ "; boundary=" + BOUNDARY);
		StringBuilder sb = new StringBuilder();
		// 上传的表单参数部分，格式请参考文章
		for (Map.Entry<String, String> entry : params.entrySet()) {// 构建表单字段内容
			sb.append("–");
			sb.append(BOUNDARY);
			sb.append("\r\n");
			sb.append("Content-Disposition: form-data; name=\
					+ entry.getKey() + "\"\r\n\r\n");
			sb.append(entry.getValue());
			sb.append("\r\n");
		}
		DataOutputStream outStream = new DataOutputStream(
				conn.getOutputStream());
		outStream.write(sb.toString().getBytes());// 发送表单字段数据
		// 上传的文件部分，格式请参考文章
		for (FormFile file : files) {
			StringBuilder split = new StringBuilder();
			split.append("–");
			split.append(BOUNDARY);
			split.append("\r\n");
			split.append("Content-Disposition: form-data;name=\
					+ file.getFormname() + "\";filename=\
					+ file.getFilname() + "\"\r\n");
			split.append("Content-Type: " + file.getContentType()
					+ "\r\n\r\n");
			outStream.write(split.toString().getBytes());
			outStream.write(file.getData(), 0, file.getData().length);
			outStream.write("\r\n".getBytes());
		}
		byte[] end_data = ("–" + BOUNDARY + "–\r\n").getBytes();// 数据结束标志
		outStream.write(end_data);
		outStream.flush();
		int cah = conn.getResponseCode();
		if (cah != 200)
			throw new RuntimeException("请求url失败");
		InputStream is = conn.getInputStream();
		int ch;
		StringBuilder b = new StringBuilder();
		while ((ch = is.read()) != -1) {
			b.append((char) ch);
		}
		outStream.close();
		conn.disconnect();
		return b.toString();
	} catch (Exception e) {
		throw new RuntimeException(e);
	}
}
```