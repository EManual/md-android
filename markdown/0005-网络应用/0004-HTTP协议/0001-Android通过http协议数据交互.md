方式一：HttpPost（import org.apache.http.client.methods.HttpPost）
```  
private Button button1, button2, button3;
private TextView textView1;
button1.setOnClickListener(new Button.OnClickListener() {
@Override
public void onClick(View arg0) {
	String uriAPI = "";
	/* 建立HTTP Post连线 */
	HttpPost httpRequest = new HttpPost(uriAPI);
	// Post运作传送变数必须用NameValuePair[]阵列储存
	// 传参数 服务端获取的方法为request.getParameter("name")
	List<NameValuePair> params = new ArrayList<NameValuePair>();
	params.add(new BasicNameValuePair("name", "this is post"));
	try {
		// 发出HTTP request
		httpRequest.setEntity(new UrlEncodedFormEntity(params,
				HTTP.UTF_8));
		// 取得HTTP response
		HttpResponse httpResponse = new DefaultHttpClient()
				.execute(httpRequest);
		// 若状态码为200 ok
		if (httpResponse.getStatusLine().getStatusCode() == 200) {
			// 取出回应字串
			String strResult = EntityUtils.toString(httpResponse
					.getEntity());
			textView1.setText(strResult);
		} else {
			textView1.setText("Error Response
					+ httpResponse.getStatusLine().toString());
		}
	} catch (ClientProtocolException e) {
		textView1.setText(e.getMessage().toString());
		e.printStackTrace();
	} catch (UnsupportedEncodingException e) {
		textView1.setText(e.getMessage().toString());
		e.printStackTrace();
	} catch (IOException e) {
		textView1.setText(e.getMessage().toString());
		e.printStackTrace();
	}
}
}); 
```
方式二：HttpURLConnection、URL(import java.net.HttpURLConnection;import java.net.URL;import java.net.URLEncoder;)
```  
private void httpUrlConnection() {
	try {
		String pathUrl = "http://172.20.0.206:8082/TestServelt/login.do";
		// 建立连接
		URL url = new URL(pathUrl);
		HttpURLConnection httpConn = (HttpURLConnection) url
				.openConnection();
		// //设置连接属性
		httpConn.setDoOutput(true);// 使用 URL 连接进行输出
		httpConn.setDoInput(true);// 使用 URL 连接进行输入
		httpConn.setUseCaches(false);// 忽略缓存
		httpConn.setRequestMethod("POST");// 设置URL请求方法
		String requestString = "客服端要以以流方式发送到服务端的数据...";
		// 设置请求属性
		// 获得数据字节数据，请求数据流的编码，必须和下面服务器端处理请求流的编码一致
		byte[] requestStringBytes = requestString.getBytes(ENCODING_UTF_8);
		httpConn.setRequestProperty("Content-length",
				+ requestStringBytes.length);
		httpConn.setRequestProperty("Content-Type",
				"application/octet-stream");
		httpConn.setRequestProperty("Connection", "Keep-Alive");// 维持长连接
		httpConn.setRequestProperty("Charset", "UTF-8");
		String name = URLEncoder.encode("黄武艺", "utf-8");
		httpConn.setRequestProperty("NAME", name);
		// 建立输出流，并写入数据
		OutputStream outputStream = httpConn.getOutputStream();
		outputStream.write(requestStringBytes);
		outputStream.close();
		// 获得响应状态
		int responseCode = httpConn.getResponseCode();
		if (HttpURLConnection.HTTP_OK == responseCode) {// 连接成功
			// 当正确响应时处理数据
			StringBuffer sb = new StringBuffer();
			String readLine;
			BufferedReader responseReader;
			// 处理响应流，必须与服务器响应流输出的编码一致
			responseReader = new BufferedReader(new InputStreamReader(
					httpConn.getInputStream(), ENCODING_UTF_8));
			while ((readLine = responseReader.readLine()) != null) {
				sb.append(readLine).append("\n");
			}
			responseReader.close();
			tv.setText(sb.toString());
		}
	} catch (Exception ex) {
		ex.printStackTrace();
	}
} 
```