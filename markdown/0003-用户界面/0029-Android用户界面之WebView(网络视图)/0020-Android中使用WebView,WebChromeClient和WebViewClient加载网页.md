在android应用中，有时要加载一个网页，如果能配上一个进度条就更好了，而android中提供了其很好的支持，下面是一个例子程序，先帖:
```  
<?xml version="1.0" encoding="utf-8"?>
<WebView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webView"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```
主程序:
```  
public class WebPageLoader extends Activity {
	final Activity activity = this;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.getWindow().requestFeature(Window.FEATURE_PROGRESS);
		setContentView(R.layout.main);
		WebView webView = (WebView) findViewById(R.id.webView);
		webView.getSettings().setJavaScriptEnabled(true);
		webView.getSettings().setSupportZoom(true);
		webView.setWebChromeClient(new WebChromeClient() {
			public void onProgressChanged(WebView view, int progress) {
				activity.setTitle("Loading...");
				activity.setProgress(progress * 100);
				if (progress == 100)
					activity.setTitle(R.string.app_name);
			}
		});
		webView.setWebViewClient(new WebViewClient() {
			public void onReceivedError(WebView view, int errorCode,
					String description, String failingUrl) { // Handle the error
			}
			public boolean shouldOverrideUrlLoading(WebView view, String url) {
				view.loadUrl(url);
				return true;
			}
		});
		webView.loadUrl("http://www.sohu.com");
	}
}
```
要注意的是,其中的webView的一系列用法,比如 webView.getSettings().setJavaScriptEnabled(true);设置可以使用javscript; 
```  
webView.getSettings().setJavaScriptEnabled(true); 
webView.setScrollBarStyle(WebView.SCROLLBARS_OUTSIDE_OVERLAY); 
webView.setHorizontalScrollBarEnabled(false); 
webView.getSettings().setSupportZoom(true); 
webView.getSettings().setBuiltInZoomControls(true); 
webView.setInitialScale(70); 
webView.setHorizontalScrollbarOverlay(true);
```
等等,具体参考API 
而进度条的使用是在new出一个setWebChromeClient后,可以在内部类中写 
onProgressChanged事件 
在WebView的设计中，不是什么事都要WebView类干的，有些杂事是分给其他人的，这样WebView专心干好自己的解析、渲染工作就行了。WebViewClient就是帮助WebView处理各种通知、请求事件的，具体来说包括： 
```  
onLoadResource 
onPageStart 
onPageFinish 
onReceiveError 
onReceivedHttpAuthRequest 
WebChromeClient是辅助WebView处理Javascript的对话框，网站图标，网站title，加载进度等 
onCloseWindow(关闭WebView) 
onCreateWindow() 
onJsAlert (WebView上alert是弹不出来东西的，需要定制你的WebChromeClient处理弹出) 
onJsPrompt 
onJsConfirm 
onProgressChanged 
onReceivedIcon 
onReceivedTitle 
```