1、添加权限：AndroidManifest.xml中必须使用许可”android.permission.INTERNET”,否则会出Web page not available错误。
2、在要Activity中生成一个WebView组件：WebView webView = new WebView(this);
3、设置WebView基本信息：
如果访问的页面中有Javascript，则webview必须设置支持Javascript。
```  
webview.getSettings().setJavaScriptEnabled(true);
```
触摸焦点起作用
```  
requestFocus();//如果不设置，则在点击网页文本输入框时，不能弹出软键盘及不响应其他的一些事件。
```
取消滚动条
```  
this.setScrollBarStyle(SCROLLBARS_OUTSIDE_OVERLAY);
```
4、设置WevView要显示的网页：
互联网用：webView.loadUrl(“http://www.google.com“);
本地文件用：webView.loadUrl(“file:///android_asset/XX.html“);  本地文件存放在：assets文件中
5、如果希望点击链接由自己处理，而不是新开Android的系统browser中响应该链接。
给WebView添加一个事件监听对象（WebViewClient)并重写其中的一些方法shouldOverrideUrlLoading：对网页中超链接按钮的响应。
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
public boolean onKeyDown(int keyCoder, KeyEvent event) {
	if (webView.canGoBack() && keyCoder == KeyEvent.KEYCODE_BACK) {
		webview.goBack(); // goBack()表示返回webView的上一页面
		return true;
	}
	return false;
}
```
Android的webView很强大，其实就是一个浏览器，你可以把它嵌入到你想要的位置，我这里遇到两个问题，就是怎么知道网页的加载进度和加载网页时，点击网页里面的链接还是在当前的webview里跳转，不想跳到浏览器那边，解决办法如下：
```  
// 此方法可以处理webview 在加载时和加载完成时一些操作
webView.setWebChromeClient(new WebChromeClient() {
	@Override
	public void onProgressChanged(WebView view, int newProgress) {
		if (newProgress == 100) {
			// 这里是设置activity的标题， 也可以根据自己的需求做一些其他的操作
			title.setText("加载完成");
		} else {
			title.setText("加载中…….");
		}
	}
});
webView.setWebViewClient(new WebViewClient() {
	@Override
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		// 重写此方法表明点击网页里面的链接还是在当前的webview里跳转，不跳到浏览器那边
		view.loadUrl(url);
		return true;
	}
	@Override
	public void onReceivedSslError(WebView view,
			SslErrorHandler handler, android.net.http.SslError error) {
		// 重写此方法可以让webview处理https请求
		handler.proceed();
	}
});
```