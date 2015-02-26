<!-- 最近接触WebView比较多，总结一下使用过程中遇到的一些问题和解决办法 -->
1. WebView无法缓存(Cache)
如果页面的Header包含了以下字段就会导致无法缓存（具体可参考CacheManger.parseHeaders()方法）：
Cache-Control: no-store, no-cache
Pragma: no-cache
需要由服务器更改页面属性解决。
2. WebView.getFavIcon()无法获取到页面图标
必须先执行以下操作（打开图标数据库）:
```  
WebIconDatabase.getInstance().open(getDir("icons" , MODE_PRIVATE ).getPath());
```
<参考链接>
3. WebViewClient.shouldOverLoadingUrl()方法有时不会被回调
我是在访问百度手机版切换为传统版时遇到的，页面 最下面的<传统版>切换：
```  
<a href="http://video.baidu.com/index.html?fr=video" target="_blank">传统版</a>  
```
网上搜集到讨论该问题的一些链接：
second call to WebView.loadUrl() no longer calls WebViewClient.shouldOverrideUrlLoading
Issue 812:WebViewClientshouldOverrideUrlLoading() not called for local-looking links
Issue 9122:WebViewClient::shouldOverrideUrlLoading dont catch form interactions with method="POST"
shouldOverrideUrlLoading(…) not executed if “window.location.href” modified in a timeout callback
目前还没法办解决，对于想截获地址并禁止其访问的，可以在WebViewClient.onPageStart()里处理：
```  
class MyWebViewClient extends WebViewClient {
	 /**
	  * 网页开始加载
	  */
	publicvoidonPageStarted(WebView view, String url, Bitmap favicon) {
		if (IsIgnoreWebsite(url)) {
			view.stopLoading();
			// 提示网页被屏蔽?
			return;
		}
	}
}
```
4. 垂直滚动条总是显示白色轨迹底图(无法消掉)
在xml中给WebView设置一下属性发觉不起一点作用：
```  
android:fadeScrollbars = "true"
android:scrollbarStyle = "outsideOverlay"
android:scrollbarAlwaysDrawVerticalTrack = "false"
```
必须在代码中对WebView进行设置才能奏效：
```  
webview.setScrollbarFadingEnabled( true );
webview.setScrollBarStyle(View. SCROLLBARS_INSIDE_OVERLAY );
```
5. 加载报错(无法创建数据库导致空指针)
这不是必然的。我的情况是，我有两个应用使用到WebView，代码都是一样的，但是其中一个死活报错 。
什么都不做，仅仅构造了WebView对象：
```  
sqlite returned: error code = 14, msg = cannot open file at source line 25467
sqlite3_open_v2("/data/data/com.demo.webview/databases/webview.db", &handle, 6, NULL) failed
sqlite returned: error code = 14, msg = cannot open file at source line 25467
sqlite3_open_v2("/data/data/com. demo .webview/databases/webviewCache.db", &handle, 6, NULL) failed
```
使用load加载网页，加载完毕时报错： 
```  
FATAL EXCEPTION: WebViewWorkerThread
java.lang.NullPointerException
  at android.webkit.WebViewDatabase.getCacheTotalSize(WebViewDatabase.java:734)
  at android.webkit.CacheManager.trimCacheIfNeeded(CacheManager.java:548)
  at android.webkit.WebViewWorker.handleMessage(WebViewWorker.java:190)
  at android.os.Handler.dispatchMessage(Handler.java:99)
  at android.os.Looper.loop(Looper.java:123)
  at android.os.HandlerThread.run(HandlerThread.java:60)
```
初步分析：
这是因为底层库打开数据库时失败，导致进一步调用该数据库对象进行操作，抛出空指针错误。
目前已尝试的解决方式：
1.捕获抛出的异常 —— 无法捕获到。