在Android中，除了使用java.net包下的API访问HTTP服务之外，我们还可以换一种途径去完成工作.Android SDK附带了Apache的HttpClient API.Apache HttpClient是一个完善的HTTP客户端，它提供了对HTTP协议的全面支持，可以使用HTTP GET和POST进行访问.下面我们就结合实例，介绍一下HttpClient的使用方法.
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package=""
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <!-- 配置测试要使用的类库 -->
        <uses-library android:name="android.test.runner" />
    </application>
    <!-- 配置测试设备的主类和目标包 -->
    <instrumentation
        android:name="android.test.InstrumentationTestRunner"
        android:targetPackage="com.scott.http" />
    <!-- 访问HTTP服务所需的网络权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-sdk android:minSdkVersion="8" />
</manifest>
```
然后，我们的单元测试类需要继承android.test.AndroidTestCase类，这个类本身是继承junit.framework.TestCase，并提供了getContext（）方法，用于获取Android上下文环境，这个设计非常有用，因为很多Android API都是需要Context才能完成的.
现在让我们来看一下我们的测试用例，HttpTest.java代码如下：
```  
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;
import junit.framework.Assert;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.HttpStatus;
import org.apache.http.NameValuePair;
import org.apache.http.client.HttpClient;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.mime.MultipartEntity;
import org.apache.http.entity.mime.content.InputStreamBody;
import org.apache.http.entity.mime.content.StringBody;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.message.BasicNameValuePair;
import android.test.AndroidTestCase;
public class HttpTest extends AndroidTestCase {
	private static final String PATH = "http://192.168.1.57:8080/web";
	public void testGet() throws Exception {
		HttpClient client = new DefaultHttpClient();
		HttpGet get = new HttpGet(PATH
				+ "/TestServlet?id=1001&name=john&age=60");
		HttpResponse response = client.execute(get);
		if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
			InputStream is = response.getEntity().getContent();
			String result = inStream2String(is);
			Assert.assertEquals(result, "GET_SUCCESS");
		}
	}
	public void testPost() throws Exception {
		HttpClient client = new DefaultHttpClient();
		HttpPost post = new HttpPost(PATH + "/TestServlet");
		List<NameValuePair> params = new ArrayList<NameValuePair>();
		params.add(new BasicNameValuePair("id", "1001"));
		params.add(new BasicNameValuePair("name", "john"));
		params.add(new BasicNameValuePair("age", "60"));
		HttpEntity formEntity = new UrlEncodedFormEntity(params);
		post.setEntity(formEntity);
		HttpResponse response = client.execute(post);
		if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
			InputStream is = response.getEntity().getContent();
			String result = inStream2String(is);
			Assert.assertEquals(result, "POST_SUCCESS");
		}
	}
	public void testUpload() throws Exception {
		InputStream is = getContext().getAssets().open("books.xml");
		HttpClient client = new DefaultHttpClient();
		HttpPost post = new HttpPost(PATH + "/UploadServlet");
		InputStreamBody isb = new InputStreamBody(is, "books.xml");
		MultipartEntity multipartEntity = new MultipartEntity();
		multipartEntity.addPart("file", isb);
		multipartEntity.addPart("desc", new StringBody("this is description."));
		post.setEntity(multipartEntity);
		HttpResponse response = client.execute(post);
		if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
			is = response.getEntity().getContent();
			String result = inStream2String(is);
			Assert.assertEquals(result, "UPLOAD_SUCCESS");
		}
	}
	// 将输入流转换成字符串
	private String inStream2String(InputStream is) throws Exception {
		ByteArrayOutputStream baos = new ByteArrayOutputStream();
		byte[] buf = new byte[1024];
		int len = -1;
		while ((len = is.read(buf)) != -1) {
			baos.write(buf, 0, len);
		}
		return new String(baos.toByteArray());
	}
}
```