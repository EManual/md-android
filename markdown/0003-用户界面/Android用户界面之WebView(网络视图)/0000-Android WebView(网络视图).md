Android网路功能很强大，WebView组件支持直接加载网页，可以将其视为一个浏览器，要实现该功能，具体步骤如下
1、在布局文件中声明WebView
2、在Activity中实例化WebView
3、调用WebView的loadUrl()方法，加载指定的URL地址网页
4、为了让WebView能够响应超链接功能，调用setWebViewClient()方法，设置WebView客户端
5、为了让WebView支持回退功能，覆盖onKeyDown()方法
6、一定要注意：在AndroidManifest.xml文件中添加访问互联网的权限，否则不能显示
<uses-permission android:name="android.permission.INTERNET"/>
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.KeyEvent;
import android.webkit.WebView;
import android.webkit.WebViewClient;
public class WebViewTest extends Activity {
	private WebView webview;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		webview = (WebView) findViewById(R.id.webview);
		// 设置WebView属性，能够执行JavaScript脚本
		webview.getSettings().setJavaScriptEnabled(true);
		// 加载URL内容
		webview.loadUrl("http://www.baidu.com");
		// 设置web视图客户端
		webview.setWebViewClient(new MyWebViewClient());
	}
	// 设置回退
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		if ((keyCode == KeyEvent.KEYCODE_BACK) && webview.canGoBack()) {
			webview.goBack();
			return true;
		}
		return super.onKeyDown(keyCode, event);
	}
	// web视图客户端
	public class MyWebViewClient extends WebViewClient {
		public boolean shouldOverviewUrlLoading(WebView view, String url) {
			view.loadUrl(url);
			return true;
		}
	}
}
```
配置文件如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <WebView
        android:id="@+id/webview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```
![img](P)  