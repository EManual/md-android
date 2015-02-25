一个WebView的简单例子。
在开发过程中应该注意几点： 
1.AndroidManifest.xml中必须使用许可"android.permission.INTERNET",否则会出Web page not available错误。
2.如果访问的页面中有Javascript，则webview必须设置支持Javascript。
```  
webview.getSettings().setJavaScriptEnabled(true);  
```
3.如果页面中链接，如果希望点击链接继续在当前browser中响应，而不是新开Android的系统browser中响应该链接，必须覆盖 webview的WebViewClient对象。
```  
mWebView.setWebViewClient(new WebViewClient(){        
	public boolean shouldOverrideUrlLoading(WebView view, String url) {        
		view.loadUrl(url);
		return true;        
	}        
});   
mWebView.setWebViewClient(new WebViewClient(){     
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
public boolean onKeyDown(int keyCode, KeyEvent event) {     
	if ((keyCode == KeyEvent.KEYCODE_BACK) && mWebView.canGoBack()) {     
		mWebView.goBack();
			   return true;     
	}     
	return super.onKeyDown(keyCode, event);     
} 
```	
下一步让我们来了解一下android中webview是如何支持javascripte自定义对象的，在w3c标准中js有 window，history，document等标准对象，同样我们可以在开发浏览器时自己定义我们的对象调用手机系统功能来处理，这样使用js就可以 为所欲为了。
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
我们看addJavascriptInterface(Object obj,String interfaceName)这个方法，该方法将一个java对象绑定到一个javascript对象中，javascript对象名就是 interfaceName（demo），作用域是Global。这样初始化webview后，在webview加载的页面中就可以直接通过 javascript:window.demo访问到绑定的java对象了。来看看在html中是怎样调用的。
```  
<html>        
<mce:script language="javascript">
<!--      
	function wave() {
		document.getElementById("droid").src="android_waving.png";
	}        
// --></mce:script>        
<body>        
	<a>
		<img id="droid" src="android_normal.png" mce_src="android_normal.png"/><br>
		Click me!
	</a>
</body>        
</html>      
<html>  
```
这样在javascript中就可以调用java对象的clickOnAndroid()方法了，同样我们可以在此对象中定义很多方法（比 如发短信，调用联系人列表等手机系统功能。）,这里wave()方法是java中调用javascript的例子。
这里还有几个知识点：
1)为了让WebView从apk文件中加载assets，Android SDK提供了一个schema，前缀为"file:///android_asset/"。WebView遇到这样的schema，就去当前包中的assets目录中找内容。如上面的"file:///android_asset/demo.html" 
2)addJavascriptInterface方法中要绑定的Java对象及方法要运行另外的线程中，不能运行在构造他的线程中，这也是使用 Handler的目的。
#### 总结：
1、添加权限：AndroidManifest.xml中必须使用许可"android.permission.INTERNET",否则会出Web page not available错误。
2、在要Activity中生成一个WebView组件：WebView webView = new WebView(this);
3、设置WebView基本信息：
如果访问的页面中有Javascript，则webview必须设置支持Javascript。
```  
webview.getSettings().setJavaScriptEnabled(true); 
```
触摸焦点起作用
```  
requestFocus();
```
取消滚动条
```  
this.setScrollBarStyle(SCROLLBARS_OUTSIDE_OVERLAY);
```
4、设置WevView要显示的网页：
互联网用：webView.loadUrl("http://www.google.com"); 
本地文件用：webView.loadUrl("file:///android_asset/XX.html");  本地文件存放在：assets文件中
5、如果希望点击链接由自己处理，而不是新开Android的系统browser中响应该链接。
给WebView添加一个事件监听对象（WebViewClient)      
并重写其中的一些方法
shouldOverrideUrlLoading：对网页中超链接按钮的响应。
当按下某个连接时WebViewClient会调用这个方法，并传递参数：按下的url 
```  
onLoadResource   
onPageStart  
onPageFinish  
onReceiveError
onReceivedHttpAuthRequest
```
6、如果用webview点链接看了很多页以后，如果不做任何处理，点击系统“Back”键，整个浏览器会调用finish()而结束自身，如果希望浏览的网页回退而不是退出浏览器，需要在当前Activity中处理并消费掉该Back事件。
覆盖Activity类的onKeyDown(int keyCoder,KeyEvent event)方法。
```  
public boolean onKeyDown(int keyCoder,KeyEvent event){
	if(webView.canGoBack() && keyCoder == KeyEvent.KEYCODE_BACK){
			webview.goBack();   //goBack()表示返回webView的上一页面
			return true;
	}
	return false;
}
```