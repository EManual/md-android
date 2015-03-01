Https与Http类似，只不过Https一般是通过post请求服务器，但是Https与http不同的是Https与服务器会话是处于连接状态。http则发送请求后连接就会断开。
发送post请求代码：
```  
String query = r4 + "&pass=" + r3; // 请求参数
byte[] entitydata = query.getBytes();// 得到实体数据
HttpsURLConnection urlCon = (new URL(ticketurl)).openConnection();
urlCon.setRequestProperty("Content-Type",
		"application/x-www-form-urlencoded;charset=UTF-8");
urlCon.setRequestProperty("Content-Length",
		String.valueOf(entitydata.length));
((HttpsURLConnection) urlCon).setRequestMethod("POST");
urlCon.setDoOutput(true);
urlCon.setDoInput(true);
urlCon.connect();
// 把封装好的实体数据发送到输出流
OutputStream outStream = urlCon.getOutputStream();
outStream.write(entitydata);
outStream.flush();
outStream.close();
// 服务器返回输入流并读写
BufferedReader in = new BufferedReader(new InputStreamReader(
		urlCon.getInputStream()));
String line;
while ((line = in.readLine()) != null) {
	return line;
}
in.close();
```
另外使用HttpsURLConnection时需要实现HostnameVerifier 和 X509TrustManager，这两个实现是必须的，要不会报安全验证异常。然后初始化X509TrustManager中的SSLContext，为javax.net.ssl.HttpsURLConnection设置默认的SocketFactory和HostnameVerifier。代码如下：
```  
private myX509TrustManager xtm = new myX509TrustManager();
private myHostnameVerifier hnv = new myHostnameVerifier();
public HttpsURLConnectionTest() {
	// 初始化X509TrustManager中的SSLContext
	SSLContext sslContext = null;
	try {
		sslContext = SSLContext.getInstance("TLS");
		X509TrustManager[] xtmArray = new X509TrustManager[] { xtm };
		sslContext.init(null, xtmArray, new java.security.SecureRandom());
	} catch (GeneralSecurityException gse) {

	}
	// 为javax.net.ssl.HttpsURLConnection设置默认的SocketFactory和HostnameVerifier
	if (sslContext != null) {
		HttpsURLConnection.setDefaultSSLSocketFactory(sslContext
				.getSocketFactory());
	}
}
HttpsURLConnection.setDefaultHostnameVerifier(hnv);
```