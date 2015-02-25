为了使页面点击链接是自己处理，所以需要给webview设置webviewclient并从写里边的方法，
```  
@Override
public boolean shouldOverrideUrlLoading(WebView view, String url) {  		
	//重写此方法表明点击网页里面的链接还是在当前的webview里跳转，不跳到浏览器那边
	view.loadUrl(url);
	return true;
}
@Override
public void onReceivedSslError(WebView view, SslErrorHandler handler, android.net.http.SslError error) { 
	// 重写此方法可以让webview处理https请求
	handler.proceed();
}
```