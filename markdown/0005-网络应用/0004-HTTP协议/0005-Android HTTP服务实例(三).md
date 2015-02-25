服务端使用apache开源项目FileUpload进行处理，所以我们需要commons-fileupload和commons-io这两个项目的jar包。
介绍完上面的三种不同的情况之后，我们需要考虑一个问题，在实际应用中，我们不能每次都新建HttpClient，而是应该只为整个应用创建一个HttpClient，并将其用于所有HTTP通信.此外，还应该注意在通过一个HttpClient同时发出多个请求时可能发生的多线程问题.针对这两个问题，我们需要改进一下我们的项目：
1.扩展系统默认的Application，并应用在项目中.
2.使用HttpClient类库提供的ThreadSafeClientManager来创建和管理HttpClient.
改进后的项目结构如图：
其中MyApplication扩展了系统的Application，代码如下：
```  
import org.apache.http.HttpVersion;
import org.apache.http.client.HttpClient;
import org.apache.http.conn.ClientConnectionManager;
import org.apache.http.conn.scheme.PlainSocketFactory;
import org.apache.http.conn.scheme.Scheme;
import org.apache.http.conn.scheme.SchemeRegistry;
import org.apache.http.conn.ssl.SSLSocketFactory;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.impl.conn.tsccm.ThreadSafeClientConnManager;
import org.apache.http.params.BasicHttpParams;
import org.apache.http.params.HttpParams;
import org.apache.http.params.HttpProtocolParams;
import org.apache.http.protocol.HTTP;
import android.app.Application;
public class MyApplication extends Application {
	private HttpClient httpClient;
	@Override
	public void onCreate() {
		super.onCreate();
		httpClient = this.createHttpClient();
	}
	@Override
	public void onLowMemory() {
		super.onLowMemory();
		this.shutdownHttpClient();
	}
	@Override
	public void onTerminate() {
		super.onTerminate();
		this.shutdownHttpClient();
	}
	// 创建HttpClient实例
	private HttpClient createHttpClient() {
		HttpParams params = new BasicHttpParams();
		HttpProtocolParams.setVersion(params, HttpVersion.HTTP_1_1);
		HttpProtocolParams.setContentCharset(params,
				HTTP.DEFAULT_CONTENT_CHARSET);
		HttpProtocolParams.setUseExpectContinue(params, true);
		SchemeRegistry schReg = new SchemeRegistry();
		schReg.register(new Scheme("http", PlainSocketFactory
				.getSocketFactory(), 80));
		schReg.register(new Scheme("https",
				SSLSocketFactory.getSocketFactory(), 443));
		ClientConnectionManager connMgr = new ThreadSafeClientConnManager(
				params, schReg);
		return new DefaultHttpClient(connMgr, params);
	}
	// 关闭连接管理器并释放资源
	private void shutdownHttpClient() {
		if (httpClient != null && httpClient.getConnectionManager() != null) {
			httpClient.getConnectionManager().shutdown();
		}
	}
	// 对外提供HttpClient实例
	public HttpClient getHttpClient() {
		return httpClient;
	}
}
```
我们重写了onCreate（）方法，在系统启动时就创建一个HttpClient；重写了onLowMemory（）和onTerminate（）方法，在内存不足和应用结束时关闭连接，释放资源.需要注意的是，当实例化DefaultHttpClient时，传入一个由ThreadSafeClientConnManager创建的一个ClientConnectionManager实例，负责管理HttpClient的HTTP连接.
然后，想要让我们这个加强版的“Application”生效，需要在AndroidManifest.xml中做如下配置：
```  
<application android:name=".MyApplication" ...> 
	.... 
</application> 
```
如果我们没有配置，系统默认会使用android.app.Application，我们添加了配置，系统就会使用我们的com.scott.http.MyApplication，然后就可以在context中调用getApplication（）来获取MyApplication实例.
有了上面的配置，我们就可以在活动中应用了，HttpActivity.java代码如下：
```  
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import org.apache.http.HttpResponse;
import org.apache.http.HttpStatus;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
public class HttpActivity extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button btn = (Button) findViewById(R.id.btn);
		btn.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				execute();
			}
		});
	}
	private void execute() {
		try {
			MyApplication app = (MyApplication) this.getApplication(); // 获取MyApplication实例
			HttpClient client = app.getHttpClient(); // 获取HttpClient实例
			HttpGet get = new HttpGet(
					"http://192.168.1.57:8080/web/TestServlet?id=1001&name=john&age=60");
			HttpResponse response = client.execute(get);
			if (response.getStatusLine().getStatusCode() == HttpStatus.SC_OK) {
				InputStream is = response.getEntity().getContent();
				String result = inStream2String(is);
				Toast.makeText(this, result, Toast.LENGTH_LONG).show();
			}
		} catch (Exception e) {
			e.printStackTrace();
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