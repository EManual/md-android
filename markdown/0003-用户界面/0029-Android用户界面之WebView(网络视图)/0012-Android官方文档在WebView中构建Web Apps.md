#### 概览
在你的Android应用布局中使用 WebView 来展现web页面
你可以创建从Javascript到客户端Android代码的接口
#### 文档内容
将 WebView 加入你的应用
在Webview中使用 JavaScript
#### 启用 JavaScript
将 JavaScript代码绑定到Android代码
处理页面导航
历史记录导航
#### 关键的类
```  
WebView
WebSettings
WebViewClient
```
#### 相关手册
Web View
如果你想发布一个web app（或者仅仅是一个web页面）作为客户端的一部分，你可以使用WebView。WebView是Android中View的扩展，能让你将web页面作为你的活动布局（activity layout）。它不包含一个浏览器的完整功能，比如导航控制或者地址栏。 WebView默认做的仅仅是展现一个Web页面。
使用WebView的一个常见场景是当你想要在应用中提供一些你可能需要更新的信息的时候，比如终端用户协议或者用户指南。在你的Android应用中，你需要创建包含WebView的Activity ，然后利用它来展现你挂在网上的文档。
另外一个使用WebView的场景是你为用户提供的数据时需要连接网络来获取数据，比如email。在这种情况下，你可能会发现在Android应用中构建一个WebView来展现提供相关数据的web页面更为容易，而不是试图连接到网络获取数据，解析数据并将其安置到Android布局中。你可以设计一个专供Android设备使用的web页面，并在Android中实现一个WebView来加载这个页面。
该文档展示了你可以如何开始使用WebView并额外做一些事情，比如页面导航、将web页面中的Javascript代码绑定到你的Android应用中的代码上去。
将WebView加入你的应用
要在你的应用中加入WebView，只需要在你的活动布局中加入<WebView>元素即可。例如，下面是一个布局文件，在这个文件中，WebView占满了屏幕。
```  
<?xml version="1.0" encoding="utf-8"?>
<WebView xmlns:android="http://schemas.android.com/apk/res/android
	android:id="@+id/webview
	android:layout_width="fill_parent
	android:layout_height="fill_parent
/>
```
要在 WebView加载页面， 使用 loadUrl()。例如： 
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
myWebView.loadUrl("http://www.example.com");
```
在它有效工作之前，你要保证你的应用能访问网络。要访问网络，需要在你的配置文件中获取INTERNET许可。例如： 
```  
<manifest ... >
<uses-permission android:name="android.permission.INTERNET" />
...
</manifest>
```
就是你要应用一个WebView 来展现web页面基本的所要做的所有事情了。在WebView中使用Javascript
如果你想要你加载在WebView中的web页面使用Javascript，你需要在WebView中启用Javascript。一旦启用Javascript，你就可以在你的应用代码以及你的Javascript代码间创建接口了。启用JavaScript你可以通过WebView中带有的 WebSettings来启用它。你可以通过getSettings()来获取 WebSettings的值，然后通过setJavaScriptEnabled()来启用Javascript。例如： 
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
WebSettings webSettings = myWebView.getSettings();
webSettings.setJavaScriptEnabled(true);
```
WebSettings 还提供了很多其他有用的设置。比如，如果你在开发一个专用于Android应用中 WebView 的web app，那么你就可以通过setUserAgentString()定义自定义用户代理字符串（custom user agent string），然后通过在web页面中查询自定义用户代理来确认正在请求你的web页面的客户端确实是Android应用。将JavaScript代码绑定到Android 代码在开发专用于Android应用中 WebView 的web app时，你可以在你的Javascript代码和客户端的Android代码间创建接口。例如，你的Javascript代码可以调用Android代码中的方法来展示一个Dialog，而不是使用Javascript中的alert()函数。为了在你的Javascript和Android代码间绑定一个新的接口，需要调用addJavascriptInterface()，传给它一个类实例来绑定到Javascript，以及一个接口名让Javascript可以调用以便来访问类。例如：你可以在你的Android应用中包括如下类： 
```  
public class JavaScriptInterface {
	Context mContext;
	 /** Instantiate the interface and set the context */
	JavaScriptInterface(Context c) {
		mContext = c;
	}
	 /** Show a toast from the web page */
	public void showToast(String toast) {
		Toast.makeText(mContext, toast, Toast.LENGTH_SHORT).show();
	}
}
```
在这个例子中，JavaScriptInterface让web页面可以使用showToast()方法来创建一个Toast消息。你可以通过addJavascriptInterface()绑定这个类到在WebView 运行的Javascript，并将接口命名为Android。例如： 
```  
WebView webView = (WebView) findViewById(R.id.webview); 
webView.addJavascriptInterface(new JavaScriptInterface(this), "Android");
```
这段代码为在WebView 运行的Javascript创建了一个名为Android的接口。这时候，你的web app就能访问JavaScriptInterface 
```  
<input type="button" value="Say hello" />
<script type="text/javascript">
	function showAndroidToast(toast) {
		Android.showToast(toast);
	}
</script>
```
没有必要从Javascript初始化Android接口， WebView会自动让它可以为你的web页面所用。所以，在按下按钮的时候，showAndroidToast() 函数会用这个Android接口来调用JavaScriptInterface.showToast()方法。注意：绑定到你的Javascript的对象在另一个线程中运行，而不是在创建它的线程中运行。
小心：使用addJavascriptInterface()可以让Javascript控制你的Android应用。这是一把双刃剑，有用的同时也可能带来安全威胁。当WebView中的HTML不可信时（例如，HTML的部分或者全部都是由一个未知的人或者进程提供的），那么一个攻击者就可能使用HTML来执行客户端的任何他想要的代码。因此，不应该使用addJavascriptInterface()，除非WebView中的所有HTML以及Javascript都是你自己写的。你同样不应该让用户在你的WebView可以定向到另外一个不是你自己的web页面上去（相反，让用户的默认浏览器应用打开外部链接——用户浏览器默认打开所有URL链接，因此一定要小心处理页面导航，像下面一节所描述的那样。）
处理页面导航
当用户点击一个WebView中的页面的链接时，默认是让Android启动一个可以处理URL的应用。通常，是由默认的浏览器打开并加载目标URL的。然而，你可以在WebView中覆盖这一行为，那么链接就会在WebView中打开。这样，你就可以让用户通过保存在WebView中的浏览记录前进或者后退了。要想让用户可以通过点击打开链接，只需要使用 setWebViewClient()为WebView提供一个 WebViewClient即可。例如： 
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
myWebView.setWebViewClient(new WebViewClient());
```
这样就可以了。现在所有用户点击的链接都会直接在WebView中加载了。如果你想要对于加载的链接的位置有更多控制，你可以创建自己的WebViewClient，覆盖 shouldOverrideUrlLoading()方法。例如： 
```  
private class MyWebViewClient extends WebViewClient {
	@Override
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		if (Uri.parse(url).getHost().equals("www.example.com")) {
			// This is my web site, so do not override; let my WebView load the
			// page
			return false;
		}
		// Otherwise, the link is not for a page on my site, so launch another
		// Activity that handles URLs
		Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
		startActivity(intent);
		return true;
	}
}
```
然后为 WebView创建一个新的 WebViewClient的实例。 
```  
WebView myWebView = (WebView) findViewById(R.id.webview);
myWebView.setWebViewClient(new MyWebViewClient());
```
现在当用户点击链接的时候，系统会调用shouldOverrideUrlLoading()，来检查URL host是否和某个特定的域匹配（如上面的定义）。如果匹配，那么该方法就返回false，不去覆盖URL加载（它仍然让WebView 像往常一样加载URL）。如果不匹配，那么就会创建一个Intent来加载默认活动（default Activity）来处理URLs（通过用户默认的web浏览器解析）。历史记录导航当你的 WebView覆盖了URL加载，它会自动生成历史访问记录。你可以通过 goBack() 或 goForward()向前或向后访问已访问过的站点。例如，下面的代码实现了通过 Activity来利用设备的后退按钮来向后导航： 
```  
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
	// Check if the key event was the BACK key and if there's history
	if ((keyCode == KeyEvent.KEYCODE_BACK) && myWebView.canGoBack() {
		myWebView.goBack();
		return true;
	}
	// If it wasn't the BACK key or there's no web page history, bubble up to the default
	// system behavior (probably exit the activity)
	return super.onKeyDown(keyCode, event);
}
```
如果有历史访问记录可供访问，canGoBack()方法会返回true。类似地，你可以使用canGoForward()来检查是否有向前访问历史。如果你不做这个检查，那么一旦用户访问到历史记录最后一项，goBack() 或 goForward()什么都不会做。