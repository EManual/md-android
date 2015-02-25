WebView使用:
```  
@Override
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	// 在标题栏上显示进度
	getWindow().requestFeature(Window.FEATURE_PROGRESS);
	// 定义WebView
	webview = new WebView(this);
	setContentView(webview);
	// 滚动条风格
	webview.setScrollBarStyle(0);
	// 设置JS可用
	webview.getSettings().setJavaScriptEnabled(true);
	final Activity activity = this;
	/*
	 [Tags]* WebChromeClient类:用来辅助WebView处理JavaScript的对话框,网站图标,网站Title,加载进度等
	 [Tags]* 通过setWebChromeClient调协WebChromeClient类
	 [Tags]*/
	webview.setWebChromeClient(new WebChromeClient() {
		// 加载进度中,100时停止
		public void onProgressChanged(WebView view, int progress) {
			activity.setProgress(progress * 100);
		}
		@Override
		public void onReceivedTitle(WebView view, String title) {
			activity.setTitle(title);
		}
	});
	/*
	 [Tags]* WebViewClient类: 用来辅助WebView处理各种通知,请求等事件的类
	 [Tags]* 通过setWebViewClient设置WebViewClient类
	 [Tags]*/
	webview.setWebViewClient(new WebViewClient() {
		// 页面加载失败
		public void onReceivedError(WebView view, int errorCode,
				String description, String failingUrl) {
			Toast.makeText(activity, "异常:! " + description,
					Toast.LENGTH_LONG).show();
		}
	});
	webview.loadUrl(Url);
}
/*
 [Tags]* 通过WebView的goBack(),goForward()方法设置其前进和后退
 [Tags]*/
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
	if (keyCode == KeyEvent.KEYCODE_BACK && webview.canGoBack()) {
		// 返回前一个页面
		webview.goBack();
		return true;
	}
	return super.onKeyDown(keyCode, event);
}
```
layout:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
</LinearLayout>
```