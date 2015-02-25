使用WebView控件 与其他控件的使用方法相同 在layout中使用一个<WebView>标签。
WebView不包括导航栏，地址栏等完整浏览器功能，只用于显示一个网页。
在WebView中加载Web页面，使用loadUrl()。
Java代码：
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
myWebView.loadUrl("http：//www.example.com");
```
注意在manifest文件中加入访问互联网的权限：
Xml代码
```  
<uses-permission android：name="android.permission.INTERNET" />
```
在Android中点击一个链接，默认是调用应用程序来启动，因此WebView需要代为处理这个动作 通过WebViewClient
Java代码：
```  
//设置WebViewClient
webView.setWebViewClient(new WebViewClient(){
	public boolean shouldOverrideUrlLoading(WebView view， String url) {
		view.loadUrl(url);
		return true;
	}
	public void onPageFinished(WebView view， String url) {
		super.onPageFinished(view， url);
	}
	public void onPageStarted(WebView view， String url， Bitmap favicon) {
		super.onPageStarted(view， url， favicon);
	}
});
```
这个WebViewClient对象是可以自己扩展的，例如
Java代码：
```  
private class MyWebViewClient extends WebViewClient {
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		if (Uri.parse(url).getHost().equals("www.example.com")) {
		return false;
	}
	Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
		startActivity(intent);
		return true;
	}
}
```
之后：
Java代码：
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
myWebView.setWebViewClient(new MyWebViewClient());
```
另外出于用户习惯上的考虑 需要将WebView表现得更像一个浏览器，也就是需要可以回退历史记录
因此需要覆盖系统的回退键 goBack，goForward可向前向后浏览历史页面
Java代码：
```  
public boolean onKeyDown(int keyCode, KeyEvent event) {
	if ((keyCode == KeyEvent.KEYCODE_BACK) && myWebView.canGoBack() {
		myWebView.goBack();
		return true;
	}
	return super.onKeyDown(keyCode， event);
}
```
Java代码：
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
WebSettings webSettings = myWebView.getSettings();
webSettings.setJavaScriptEnabled(true);
```
(这里的webSetting用处非常大 可以开启很多设置 在之后的本地存储，地理位置等之中都会使用到) 
1，在JS中调用Android的函数方法，首先需要在Android程序中建立接口
Java代码
代码片段，双击复制
```  
final class InJavaScript {
	public void runOnAndroidJavaScript(final String str) {
		handler.post(new Runnable() {
			public void run() {
				TextView show = (TextView) findViewById(R.id.textview);
				show.setText(str);
			}
		});
	}
}
```
Java代码
```  
//把本类的一个实例添加到js的全局对象window中，
//这样就可以使用windows.injs来调用它的方法
webView.addJavascriptInterface(new InJavaScript(), "injs");
```
在JavaScript中调用
Js代码
```  
function sendToAndroid(){
	var str = "Cookie call the Android method from js";
	windows.injs.runOnAndroidJavaScript(str);//调用android的函数
}
```
2 在Android中调用JS的方法   在JS中的方法：
Js代码
```  
function getFromAndroid(str){
	document.getElementByIdx_x_x_x("android").innerHTML=str;
}
```
在Android调用该方法
代码片段，双击复制
```  
Button button = (Button) findViewById(R.id.button);
button.setOnClickListener(new OnClickListener() {
	public void onClick(View arg0) {
		//调用javascript中的方法
		webView.loadUrl("javascript??getFromAndroid('Cookie call the js function from Android')");
	}
});
```