提供了这样的API，WebView可以和JavaScript之间进行通信，这样Native代码就能很好的和本地或者远程服务器上的Html进行交互。写了一个最简单的例子，WebView和本地的Html代码进行交互。效果如下：
![img](P)  
点击buttons按钮，红色框中的test变成了Activity中传递的数据：
![img](P)  
下图是工程的目录结构，其中demo.html就是本地html：
![img](P)  
实现步骤如下，首先创建Android工程，修改main.xml文件，加入WebView标签：
```  
<linearlayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <webview
        android:id="@+id/webview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</linearlayout>
```
然后修改MainActivity，使其加载webview，并且和html进行通信：
```  
private WebView mWebView;
@Override
public void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	setContentView(R.layout.main);
	mWebView = (WebView) findViewById(R.id.webview);
	WebSettings webSettings = mWebView.getSettings();
	webSettings.setSavePassword(false);
	webSettings.setSaveFormData(false);
	webSettings.setJavaScriptEnabled(true);
	webSettings.setSupportZoom(true);
	mWebView.addJavascriptInterface(new DemoJavaScriptInterface(), "demo");
	mWebView.loadUrl("file:///android_asset/demo.html");
}
final class DemoJavaScriptInterface {
	DemoJavaScriptInterface() {
	}
	public int mydata() {
		return 10;
	}
}
```
解释一下上面的代码，
```  
webSettings.setJavaScriptEnabled(true);
```
是否允许在webview中执行JavaScript代码，这里设置为true。
```  
mWebView.addJavascriptInterface(new DemoJavaScriptInterface(), "demo");
```
绑定Java对象到JavaScript中，这样就能在JavaScript中调用Java对象，实现通信。方法中第一个参数就是Java对象，第二个参数表示该Java对象的别名，在JavaScript中使用。
```  
mWebView.loadUrl("file:///android_asset/demo.html");
```
WebView加载本地html代码，注意本地的html代码必须放在工程的assets目录下，然后通过file:///android_asset/demo.html访问。demo.html代码如下： 
```  
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"> 
<html> 
<head> 
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> 
<title>Insert title here</title> 
</head> 
<body>这是一个html页面 
<div id="output" >test</div> 
	<input type="submit" value="buttons
		onclick="document.getElementById(’output’).innerHTML=demo.mydata()"/>
</body> 
</html>
```
看一下红色标记的代码，demo对应Java代码中的addJavascriptInterface方法中的demo，mydata()是DemoJavaScriptInterface类的一个方法。