今天我们就来讲一讲在解析socket的inputstream流时出现乱码问题，我们在这里直接拿了HTTP流实验了下
希望我这个例子对大家有一些帮助
```  
public String getHttpContent(String htmlUrl) throws IOException,
		InterruptedException {
	URL url;
	InputStream is = null;
	HttpURLConnection urlConn = null;
	int count = 0;
	ByteArrayOutputStream baos = new ByteArrayOutputStream();
	try {
		url = new URL(htmlUrl);
		urlConn = (HttpURLConnection) url.openConnection();
		urlConn.setConnectTimeout(20000);
		urlConn.setReadTimeout(20000);
		is = urlConn.getInputStream();
		byte[] buf = new byte[512];
		int ch = -1;
		while ((ch = is.read(buf)) != -1) {
			baos.write(buf, 0, ch);
			count = count + ch;
		}
	} catch (final MalformedURLException me) {
		me.getMessage();
		throw me;
	} catch (final IOException e) {
		e.printStackTrace();
		throw e;
	}
	return new String(baos.toByteArray(), "GB2312");
}
```
实上面的方法很简单，刚开始我们用的BufferedReader去读，这样直接读出来String有问题，解码不对，后来自己读到byteoutputstream里，然后读出字节自己手工编码就对了，可是昨天晚上发现了一个更简单的方法，我们真是走了一个大大的弯路，如下：
```  
public String getHttpContent(String htmlurl) throws Exception {
	HttpClient hc = new DefaultHttpClient();
	HttpGet get = new HttpGet(htmlUrl);
	HttpResponse rp = hc.execute(get);
	if (rp.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
		return EntityUtils.toString(rp.getEntity()).trim();
	} else {
		return null;
	}
}
```