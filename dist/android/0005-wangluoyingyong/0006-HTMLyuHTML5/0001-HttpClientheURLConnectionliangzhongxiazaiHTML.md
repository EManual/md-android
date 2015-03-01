我们下面要用两种方法来下载html,这两种方法分别采用HttpClient和URLConnection，好像是HttpClient方式比较稳定，一般都能下载到，但是URLConnection在EDGE网络下经常下不到数据。我们还是来看看代码吧：
```  
public String getHTML(String url) {
	try {
		URL newUrl = new URL(url);
		URLConnection connect = newUrl.openConnection();
		connect.setRequestProperty("User-Agent",
				"Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");
		DataInputStream dis = new DataInputStream(connect.getInputStream());
		BufferedReader in = new BufferedReader(new InputStreamReader(dis,
				"UTF-8"));
		// 目标页面编码为UTF-8
		String html = "";
		String readLine = null;
		while ((readLine = in.readLine()) != null) {
			html = html + readLine;
		}
		in.close();
		return html;
	} catch (MalformedURLException me) {
	} catch (IOException ioe) {
	}
	return null;
}
```
大家要记住的一个事，那就是这句话content = new String(content.getBytes("ISO-8859-1"),"UTF-8");我们大家一定要记住，我们要是有中文的话，就是utf-8。大家可千万要记住了。有了这个菜不会出现乱码。
```  
public String getHTML(String url) {
	try {
		URL newUrl = new URL(url);
		URLConnection connect = newUrl.openConnection();
		connect.setRequestProperty("User-Agent",
				"Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");
		DataInputStream dis = new DataInputStream(connect.getInputStream());
		BufferedReader in = new BufferedReader(new InputStreamReader(dis,
				"UTF-8"));
		// 目标页面编码为UTF-8
		String html = "";
		String readLine = null;
		while ((readLine = in.readLine()) != null) {
			html = html + readLine;
		}
		in.close();
		return html;
	} catch (MalformedURLException me) {
	} catch (IOException ioe) {
	}
	return null;
}
```
大家看见了吧，我们的第二种方法也是有utf-8的，这句话在代码中是非常重要的。
```  
BufferedReader in = new BufferedReader(new InputStreamReader(dis,"UTF-8"));
```
虽然这两个方法非常简单的，但是有的时候也会犯一下很低级的错误哦。就比如我就是容易爱把中文的utf-8给忘记。大家可不要走我的老路呀。大家要是有更好的方法，我们可以发帖进行交流。