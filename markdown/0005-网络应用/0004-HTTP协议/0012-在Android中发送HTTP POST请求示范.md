贴一段发送Http POST到服务器的代码，非常简单：
```  
public void MyFunction{
	HttpClient httpclient = new DefaultHttpClient();
	//你的URL
	HttpPost httppost = new HttpPost("http://www.eoeandroid.com/post_datas.php");
	try { 
	   List<NameValuePair> nameValuePairs = new ArrayList<NameValuePair>();
	   //Your DATA
	   nameValuePairs.add(new BasicNameValuePair("id", "12345"));
	   nameValuePairs.add(new BasicNameValuePair("stringdata", "eoeAndroid.com is Cool!"));
	   httppost.setEntity(new UrlEncodedFormEntity(nameValuePairs));
	   HttpResponse response;
	   response=httpclient.execute(httppost);
	} catch (ClientProtocolException e) { 
		e.printStackTrace();
	} catch (IOException e) { 
		e.printStackTrace();
	} 
}
```