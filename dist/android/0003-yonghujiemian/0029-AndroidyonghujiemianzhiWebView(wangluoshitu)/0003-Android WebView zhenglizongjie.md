在Android手机中内置了一款高性能webkit内核浏览器，在SDK中封装为一个叫做WebView组件。 
#### 什么是webkit 
WebKit是Mac OS X v10.3及以上版本所包含的软件框架（对v10.2.7及以上版本也可通过软件更新获取）。 同时，WebKit也是Mac OS X的Safari网页浏览器的基础。WebKit是一个开源项目，主要由KDE的KHTML修改而来并且包含了一些来自苹果公司的一些组件。 
传统上，WebKit包含一个网页引擎WebCore和一个脚本引擎JavaScriptCore，它们分别对应的是KDE的KHTML和KJS。不过，随着JavaScript引擎的独立性越来越强，现在WebKit和WebCore已经基本上混用不分（例如Google Chrome和Maxthon 3采用V8引擎，却仍然宣称自己是WebKit内核）。 
这里我们初步体验一下在android是使用webview浏览网页，在SDK的Dev Guide中有一个WebView的简单例子。 
在开发过程中应该注意几点： 
1.AndroidManifest.xml中必须使用许可"android.permission.INTERNET",否则会出Web page not available错误。
2.如果访问的页面中有Javascript，则webview必须设置支持Javascript。
webview.getSettings().setJavaScriptEnabled(true);  
3.如果页面中链接，如果希望点击链接继续在当前browser中响应，而不是新开Android的系统browser中响应该链接，必须覆盖 webview的WebViewClient对象。
```  
mWebView.setWebViewClient(new WebViewClient() {
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		view.loadUrl(url);
		return true;
	}
});   
```
4.如果不做任何处理，浏览网页，点击系统“Back”键，整个Browser会调用finish()而结束自身，如果希望浏览的网 页回退而不是推出浏览器，需要在当前Activity中处理并消费掉该Back事件。
```  
public boolean onKeyDown(int keyCode, KeyEvent event) {
	if ((keyCode == KeyEvent.KEYCODE_BACK) && mWebView.canGoBack()) {
		mWebView.goBack();
		return true;
	}
	return super.onKeyDown(keyCode, event);
}
```
下一步让我们来了解一下android中webview是如何支持javascripte自定义对象的，在w3c标准中js有window，history，document等标准对象，同样我们可以在开发浏览器时自己定义我们的对象调用手机系统功能来处理，这样使用js就可以为所欲为了。
看一个实例：
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
我们看addJavascriptInterface(Object obj,String interfaceName)这个方法，该方法将一个java对象绑定到一个javascript对象中，javascript对象名就是interfaceName（demo），作用域是Global。这样初始化webview后，在webview加载的页面中就可以直接通过javascript:window.demo访问到绑定的java对象了。来看看在html中是怎样调用的。
```  
<html>
<mce:script language="javascript">
<!-- 
	function wave() {
		document.getElementById("droid").src="android_waving.png";
	}  
// --></mce:script>
<body>
<a onClick="window.demo.clickOnAndroid()">
	<img id="droid
	src="android_normal.png
	mce_src="android_normal.png"/><br>
	Click me!
</a>
</body>
</html>
```
这样在javascript中就可以调用java对象的clickOnAndroid()方法了，同样我们可以在此对象中定义很多方法（比如发短信，调用联系人列表等手机系统功能。）,这里wave()方法是java中调用javascript的例子。
这里还有几个知识点： 
1)为了让WebView从apk文件中加载assets，Android SDK提供了一个schema，前缀为"file:///android_asset/"。WebView遇到这样的schema，就去当前包中的assets目录中找内容。如上面的"file:///android_asset/demo.html"。
2)addJavascriptInterface方法中要绑定的Java对象及方法要运行另外的线程中，不能运行在构造他的线程中，这也是使用Handler的目的。