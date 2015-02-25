WebView组件是 Android手机中高性能webkit内核浏览器的封装，对Javascript有良好的支持。
WebView负责解析、渲染html。
WebViewClient负责帮助WebView处理各种通知、请求事件等。
WebChromeClient负责帮助WebView处理Javascript的对话框，网站图标，网站title，加载进度等。
应用WebView组件，实现了一个aDict在线词典小实例。
API采用：http://dict.cn/mini.php?q=word。
1. web访问权限 ：
```   
<uses-permission android:name="android.permission.INTERNET" />；
```
2. 设置WebView的参数：
```  
mWebView.getSettings().setDefaultTextEncodingName("gb2312");
mWebView.getSettings().setSupportZoom(false);
```
3. 响应页面中的链接，而不是新开Android的系统browser中响应该链接，
必须覆盖WebViewClient对象的shouldOverrideUrlLoading(WebView view, String url)；
4. 当用户按下Back键时，在WebView历史页面中Back，而不是Activity退出，需要在当前Activity中处理Back事件
```  
if ((keyCode == KeyEvent.KEYCODE_BACK) && mWebView.canGoBack()) {  
	mWebView.goBack();
	return true;  
}
```
5. webview中设置Javascript支持：
```  
mWebView.getSettings().setJavaScriptEnabled(true);
```