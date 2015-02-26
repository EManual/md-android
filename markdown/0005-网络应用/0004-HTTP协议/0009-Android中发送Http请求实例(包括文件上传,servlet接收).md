前天开始要准备实现手机端往服务器传参数，还要能传附件，找了不少文章和资料，现在总结一下分享分享：代码中的catch什么的就省略了，尝试了图片、txt、xml是没问题的.. 各位尽情拍砖吧。
发完发现代码部分的格式……这个编辑器不太会用，怎么感觉把换行都去掉了,处理好换行缩进也……
首先我是写了个java工程测试发送post请求：可以包含文本参数和文件参数
```  
 /**
  * 通过http协议提交数据到服务端，实现表单提交功能，包括上传文件
  * @param actionUrl
  *            上传路径
  * @param params
  *            请求参数 key为参数名,value为参数值
  * @param file
  *            上传文件
  */
public static void postMultiParams(String actionUrl,
		Map<String, String> params, FormBean[] files) {
	try {
		PostMethod post = new PostMethod(actionUrl);
		List<art> formParams = new ArrayList<art>();
		for (Map.Entry<String, String> entry : params.entrySet()) {
			formParams.add(new StringPart(entry.getKey(), entry.getValue()));
		}
		if (files != null)
			for (FormBean file : files) {
				// filename为在服务端接收时希望保存成的文件名，filepath是本地文件路径(包括了源文件名)，filebean中就包含了这俩属性
				formParams.add(new FilePart("file", file.getFilename(),
						new File(file.getFilepath())));
			}
		Part[] parts = new Part[formParams.size()];
		Iterator<art> pit = formParams.iterator();
		int i = 0;

		while (pit.hasNext()) {
			parts[i++] = pit.next();
		}
		// 如果出现乱码可以尝试一下方式
		// StringPart sp = new StringPart("TEXT", "testValue", "GB2312");
		// FilePart fp = new FilePart("file", "test.txt", new
		// File("./temp/test.txt"), null, "GB2312"
		// postMethod.getParams().setContentCharset("GB2312");
		MultipartRequestEntity mrp = new MultipartRequestEntity(parts,
				post.getParams());
		post.setRequestEntity(mrp);
		// execute post method
		HttpClient client = new HttpClient();
		int code = client.executeMethod(post);
		System.out.println(code);
	} catch (Exception e) {
	}
}
```
通过以上代码可以成功的模拟java客户端发送post请求，服务端也能接收并保存文件
java端测试的main方法：
```  
public static void main(String[] args) {
	String actionUrl = "http://192.168.0.123:8080/WSserver/androidUploadServlet";
	Map<String, String> strParams = new HashMap<String, String>();
	strParams.put("paramOne", "valueOne");
	strParams.put("paramTwo", "valueTwo");
	FormBean[] files = new FormBean[] { new FormBean("dest1.xml",
			"F:/testpostsrc/main.xml") };
	HttpTool.postMultiParams(actionUrl, strParams, files);
}
```
本以为大功告成了，结果一移植到android工程中，编译是没有问题的。
但是运行时抛了异常 先是说找不到PostMethod类，org.apache.commons.httpclient.methods.PostMethod这个类绝对是有包含的；
还有个异常就是VerifyError。 开发中有几次碰到这个异常都束手无策，觉得是SDK不兼容还是怎么地，哪位知道可得跟我说说～～
于是看网上有直接分析http request的内容构建post请求的，也有找到带上传文件的，拿下来运行老是有些问题，便直接通过运行上面的java工程发送的post请求，在servlet中打印出请求内容，然后对照着拼接字符串和流终于给实现了！代码如下：
```  
 /**
  * 通过拼接的方式构造请求内容，实现参数传输以及文件传输
  * @param actionUrl
  * @param params
  * @param files
  * @return
  * @throws IOException
  */
public static String post(String actionUrl, Map<String, String> params,
		Map<String, File> files) throws IOException {
	String BOUNDARY = java.util.UUID.randomUUID().toString();
	String PREFIX = "--", LINEND = "\r\n";
	String MULTIPART_FROM_DATA = "multipart/form-data";
	String CHARSET = "UTF-8";
	URL uri = new URL(actionUrl);
	HttpURLConnection conn = (HttpURLConnection) uri.openConnection();
	conn.setReadTimeout(5 * 1000); // 缓存的最长时间
	conn.setDoInput(true);// 允许输入
	conn.setDoOutput(true);// 允许输出
	conn.setUseCaches(false); // 不允许使用缓存
	conn.setRequestMethod("POST");
	conn.setRequestProperty("connection", "keep-alive");
	conn.setRequestProperty("Charsert", "UTF-8");
	conn.setRequestProperty("Content-Type", MULTIPART_FROM_DATA
			+ ";boundary=" + BOUNDARY);
	// 首先组拼文本类型的参数
	StringBuilder sb = new StringBuilder();
	for (Map.Entry<String, String> entry : params.entrySet()) {
		sb.append(PREFIX);
		sb.append(BOUNDARY);
		sb.append(LINEND);
		sb.append("Content-Disposition: form-data; name=\
				+ entry.getKey() + "\"" + LINEND);
		sb.append("Content-Type: text/plain; charset=" + CHARSET + LINEND);
		sb.append("Content-Transfer-Encoding: 8bit" + LINEND);
		sb.append(LINEND);
		sb.append(entry.getValue());
		sb.append(LINEND);
	}
	DataOutputStream outStream = new DataOutputStream(
			conn.getOutputStream());
	outStream.write(sb.toString().getBytes());
	// 发送文件数据
	if (files != null)
		for (Map.Entry<String, File> file : files.entrySet()) {
			StringBuilder sb1 = new StringBuilder();
			sb1.append(PREFIX);
			sb1.append(BOUNDARY);
			sb1.append(LINEND);
			sb1.append("Content-Disposition: form-data; name=\"file\"; filename=\
					+ file.getKey() + "\"" + LINEND);
			sb1.append("Content-Type: application/octet-stream; charset=
					+ CHARSET + LINEND);
			sb1.append(LINEND);
			outStream.write(sb1.toString().getBytes());
			InputStream is = new FileInputStream(file.getValue());
			byte[] buffer = new byte[1024];
			int len = 0;
			while ((len = is.read(buffer)) != -1) {
				outStream.write(buffer, 0, len);
			}
			is.close();
			outStream.write(LINEND.getBytes());
		}
	// 请求结束标志
	byte[] end_data = (PREFIX + BOUNDARY + PREFIX + LINEND).getBytes();
	outStream.write(end_data);
	outStream.flush();
	// 得到响应码
	int res = conn.getResponseCode();
	if (res == 200) {
		InputStream in = conn.getInputStream();
		int ch;
		StringBuilder sb2 = new StringBuilder();
		while ((ch = in.read()) != -1) {
			sb2.append((char) ch);
		}
	}
	outStream.close();
	conn.disconnect();
	return in.toString();
}
```
**********************
button响应中的代码：
**********************
```  
public void onClick(View v) {
	String actionUrl = getApplicationContext().getString(
			R.string.wtsb_req_upload);
	Map<String, String> params = new HashMap<String, String>();
	params.put("strParamName", "strParamValue");
	Map<String, File> files = new HashMap<String, File>();
	files.put("tempAndroid.txt", new File("/sdcard/temp.txt"));
	try {
		HttpTool.postMultiParams(actionUrl, params, files);
	} catch (Exception e) {
	}
}
```
***************************
服务器端servlet代码：
***************************
```  
public void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
	// print request.getInputStream to check request content
	// HttpTool.printStreamContent(request.getInputStream());
	RequestContext req = new ServletRequestContext(request);
	if (FileUpload.isMultipartContent(req)) {
		DiskFileItemFactory factory = new DiskFileItemFactory();
		ServletFileUpload fileUpload = new ServletFileUpload(factory);
		fileUpload.setFileSizeMax(FILE_MAX_SIZE);
		List items = new ArrayList();
		try {
			items = fileUpload.parseRequest(request);
		} catch (Exception e) {
		}
		Iterator it = items.iterator();
		while (it.hasNext()) {
			FileItem fileItem = (FileItem) it.next();
			if (fileItem.isFormField()) {
				System.out.println(fileItem.getFieldName()
						+ "
						+ fileItem.getName()
						+ "
						+ new String(fileItem.getString().getBytes(
								"ISO-8859-1"), "GBK"));
			} else {
				System.out.println(fileItem.getFieldName() + " 
						+ fileItem.getName() + " " + fileItem.isInMemory()
						+ " " + fileItem.getContentType() + "
						+ fileItem.getSize());
				if (fileItem.getName() != null && fileItem.getSize() != 0) {
					File fullFile = new File(fileItem.getName());
					File newFile = new File(FILE_SAVE_PATH
							+ fullFile.getName());
					try {
						fileItem.write(newFile);
					} catch (Exception e) {
					}
				}
			}
		}
	}
}
public void init() throws ServletException {
	// 读取在web.xml中配置的init-param
	FILE_MAX_SIZE = Long.parseLong(this.getInitParameter("file_max_size"));// 上传文件大小限制
	FILE_SAVE_PATH = this.getInitParameter("file_save_path");// 文件保存位置
}
```