代码如下：
```  
public class WebViewDemo extends Activity {
	private WebView mWebView;
	private Handler mHandler = new Handler();
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.webviewdemo);
		mWebView = (WebView) findViewById(R.id.webview);
		WebSettings webSettings = mWebView.getSettings();
		webSettings.setJavaScriptEnabled(true);
		mWebView.addJavascriptInterface(new Object() {
			public void clickOnAndroid() {
				mHandler.post(new Runnable() {
					public void run() {
						mWebView.loadUrl("javascript:wave()");
					}
				});
			}
		}, "demo");
		mWebView.loadUrl("file:///android_asset/demo.html");
	}
}
```
这里的重点是addJavascriptInterface(Object obj,String interfaceName)方法，该方法将一个java对象绑定到一个javascript对象中，javascript对象名就是interfaceName，作用域是Global。这样初始化webview后，在webview加载的页面中就可以直接通过javascript:window.demo访问到绑定的java对象了。来看看在html中是怎样调用的：
```  
<html>
<script language=”javascript”>
	function wave() {
		document.getElementById(“droid”).src=”android_waving.png”;
	}
</script>
<body>
	<a onClick=”window.demo.clickOnAndroid()”>
		<img id=”droid” src=”android_normal.png”/><br>
		Click me!
	</a>
</body>
</html>
```
这样在javascript中就可以调用java对象的clickOnAndroid()方法了，wave()方法是java中调用javascript的例子。
这里还有几个知识点：
1)为了让WebView从apk文件中加载assets，Android SDK提供了一个schema，前缀为”file:///android_asset/”。WebView遇到这样的schema，就去当前包中的assets目录中找内容。如上面的”file:///android_asset/demo.html”
2)addJavascriptInterface方法中要绑定的Java对象及方法要运行另外的线程中，不能运行在构造他的线程中，这也是使用Handler的目的。