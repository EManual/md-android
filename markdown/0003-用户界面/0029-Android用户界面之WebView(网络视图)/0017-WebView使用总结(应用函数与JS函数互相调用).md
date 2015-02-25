1.当只用WebView的时候,最先注意的当然是在配置文件中添加访问因特网的权限; 
2.如果访问的页面中有Javascript,必须设置支持Javascript:
```  
webview.getSettings().setJavaScriptEnabled(true);
```
3.如果希望点击链接由自己处理而不是新开Android的系统browser中响应该链接.给WebView添加一个事件监听对象(WebViewClient)并重写其中的一些方法 shouldOverrideUrlLoading对网页中超链接按钮的响应 
```  
mWebView.setWebViewClient(new WebViewClient() {
	[Tags]/**
	[Tags]* Show in webview not system webview.
	[Tags]*/
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		view.loadUrl(url);
		return super.shouldOverrideUrlLoading(view, url);
	}
}
```
这样就保证了每次打开的页面都是在WebView实例中显示运行的; 
4.在显示WebView时,点击手机Back时,会完全退出当前Activity,如果想退到历史浏览页面:重写back监听: 
```  
public boolean onKeyDown(int keyCode, KeyEvent event) {
	WebView mWebView = (WebView) findViewById(R.id.browser);
	if ((keyCode == KeyEvent.KEYCODE_BACK) && mWebView.canGoBack()) {
		mWebView.goBack();
		return true;
	}
	return super.onKeyDown(keyCode, event);
}
```
5.Android SDK提供了一个schema前缀为"file:///android_asset/".WebView遇到这样的schema,就去当前包中的 assets目录中找内容.如:"file:///android_asset/demo.html" 
下面一段代码是对网页中JS的类似Alert()类的函数进行相应的重写响应: 
```  
webView.setWebChromeClient(new WebChromeClient() {
	public boolean onJsAlert(WebView view, String url, String message,
			final JsResult result) {
		AlertDialog.Builder b = new AlertDialog.Builder(BrowserJs.this);
		b.setTitle("Alert");
		b.setMessage(message);
		b.setPositiveButton(android.R.string.ok,
				new AlertDialog.OnClickListener() {
					public void onClick(DialogInterface dialog,
							int which) {
						result.confirm();
					}
				});
		b.setCancelable(false);
		b.create();
		b.show();
		return true;
	};
	@Override
	public boolean onJsConfirm(WebView view, String url,
			String message, final JsResult result) {
		AlertDialog.Builder b = new AlertDialog.Builder(BrowserJs.this);
		b.setTitle("Confirm");
		b.setMessage(message);
		b.setPositiveButton(android.R.string.ok,
				new AlertDialog.OnClickListener() {
					public void onClick(DialogInterface dialog,
							int which) {
						result.confirm();
					}
				});
		b.setNegativeButton(android.R.string.cancel,
				new DialogInterface.OnClickListener() {
					public void onClick(DialogInterface dialog,
							int which) {
						result.cancel();
					}
				});
		b.setCancelable(false);
		b.create();
		b.show();
		return true;
	};
	@Override
	public boolean onJsPrompt(WebView view, String url, String message,
			String defaultValue, final JsPromptResult result) {
		final LayoutInflater factory = LayoutInflater
				.from(BrowserJs.this);
		final View v = factory.inflate(R.layout.prompt_dialog, null);
		((TextView) v.findViewById(R.id.prompt_message_text))
				.setText(message);
		((EditText) v.findViewById(R.id.prompt_input_field))
				.setText(defaultValue);

		AlertDialog.Builder b = new AlertDialog.Builder(BrowserJs.this);
		b.setTitle("Prompt");
		b.setView(v);
		b.setPositiveButton(android.R.string.ok,
				new AlertDialog.OnClickListener() {
					public void onClick(DialogInterface dialog,
							int which) {
						String value = ((EditText) v
								.findViewById(R.id.prompt_input_field))
								.getText().toString();
						result.confirm(value);
					}
				});
		b.setNegativeButton(android.R.string.cancel,
				new DialogInterface.OnClickListener() {
					public void onClick(DialogInterface dialog,
							int which) {
						result.cancel();
					}
				});
		b.setOnCancelListener(new DialogInterface.OnCancelListener() {
			public void onCancel(DialogInterface dialog) {
				result.cancel();
			}
		});
		b.show();
		return true;
	};
	public void onProgressChanged(WebView view, int newProgress) {
		BrowserJs.this.getWindow().setFeatureInt(
				Window.FEATURE_PROGRESS, newProgress * 100);
		super.onProgressChanged(view, newProgress);
	}
	public void onReceivedTitle(WebView view, String title) {
		BrowserJs.this.setTitle(title);
		super.onReceivedTitle(view, title);
	}
});
go.setOnClickListener(new OnClickListener() {
	public void onClick(View view) {
		String url = text.getText().toString();
		webView.loadUrl(url);
	}
});
webView.loadUrl("file:///android_asset/index.html");}
```
在上述代码中,用到的prompt_dialog.xml: 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:gravity="center_horizontal"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/prompt_message_text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <EditText
        android:id="@+id/prompt_input_field"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:minWidth="250dp"
        android:scrollHorizontally="true"
        android:selectAllOnFocus="true" />
</LinearLayout>
```
还有assets中的Html文件: 
```  
<html>
<script type="text/javascript">
function onAlert(){
	alert("This is a alert sample from html");
}
function onConfirm(){
	var b=confirm("are you sure to login?");
	alert("your choice is "+b);
}
function onPrompt(){
	var b=prompt("please input your password","aaa");
	alert("your input is "+b);
}
</script>
<pre>
<input type="button" value="alert"/>
<input type="button" value="confirm"/>
<input type="button" value="prompt"/>
<a href="http://www.google.com"/>Google</a>
</pre>
</html>
```
接着上篇: 
6.通过字符串拼凑的html页面显示: 
```  
public void simpleJsClick() {
	WebView webView = (WebView) findViewById(R.id.webview);
	String html = "<html>
			+ "<body>
			+ "图书封面<br>
			+ "<table width='200' border='1' >
			+ "<tr>
			+ "<td><a onclick='alert(\"Java Web开发速学宝典\")' ><img style='margin:10px' src='http://images.china-pub.com/ebook45001-50000/48015/cover.jpg' width='100'/></a></td>
			+ "<td><a onclick='alert(\"大象--Thinking in UML\")' ><img style='margin:10px' src='http://images.china-pub.com/ebook125001-130000/129881/zcover.jpg' width='100'/></td>
			+ "</tr>
			+ "<tr>
			+ "<td><img style='margin:10px' src='http://images.china-pub.com/ebook25001-30000/27518/zcover.jpg' width='100'/></td>
			+ "<td><img style='margin:10px' src='http://images.china-pub.com/ebook30001-35000/34838/zcover.jpg' width='100'/></td>
			+ "</tr>" + "</table>" + "</body>" + "</html>";

	webView.loadDataWithBaseURL(null, html, "text/html", "utf-8", null);
	webView.getSettings().setJavaScriptEnabled(true);
	webView.setWebChromeClient(new WebChromeClient());
}
```
7.在同种分辨率的情况下,屏幕密度不一样的情况下,自动适配页面: 
```  
DisplayMetrics dm = getResources().getDisplayMetrics();
int scale = dm.densityDpi;
if (scale == 240) { //
	webView.getSettings().setDefaultZoom(ZoomDensity.FAR);
} else if (scale == 160) {
	webView.getSettings().setDefaultZoom(ZoomDensity.MEDIUM);
} else {
	webView.getSettings().setDefaultZoom(ZoomDensity.CLOSE);
}
```
8.判断加载的页面URL地址是否正确: 
```  
if(URLUtil.isNetworkUrl(url)==true)
```
9.设置WebView的一些缩放功能点: 
```  
webView.getSettings().setJavaScriptEnabled(true);
webView.setScrollBarStyle(WebView.SCROLLBARS_OUTSIDE_OVERLAY);
webView.setHorizontalScrollBarEnabled(false);
webView.getSettings().setSupportZoom(true);
webView.getSettings().setBuiltInZoomControls(true);
webView.setInitialScale(70);
webView.setHorizontalScrollbarOverlay(true);
```
完成java文件: 
```  
public class MethodMutual extends Activity {
	private WebView mWebView;
	private Handler mHandler = new Handler();
	private static final String LOG_TAG = "WebViewDemo";
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		loadAssetHtml();
	}
	public void loadAssetHtml() {
		mWebView = (WebView) findViewById(R.id.webview);
		WebSettings webSettings = mWebView.getSettings();
		webSettings.setSavePassword(false);
		webSettings.setSaveFormData(false);
		webSettings.setJavaScriptEnabled(true);
		webSettings.setSupportZoom(false);
		mWebView.setWebChromeClient(new MyWebChromeClient());
		// 将一个java对象绑定到一个javascript对象中,javascript对象名就是interfaceName,作用域是Global.
		mWebView.addJavascriptInterface(new DemoJavaScriptInterface(), "demo");
		mWebView.loadUrl("file:///android_asset/demo.html");
		// 通过应用中按钮点击触发JS函数响应
		Button mCallJS = (Button) findViewById(R.id.mCallJS);
		mCallJS.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				mWebView.loadUrl("javascript:wave()");
			}
		});
	}
	private int i = 0;
	final class DemoJavaScriptInterface {
		DemoJavaScriptInterface() {
		}
		[Tags]/**
		 [Tags]* This is not called on the UI thread. Post a runnable to invoke
		 [Tags]* loadUrl on the UI thread.
		 [Tags]*/
		public void callAndroid() {
			mHandler.post(new Runnable() {
				public void run() {
					if (i % 2 == 0) {
						mWebView.loadUrl("javascript:wave()");
					} else {
						mWebView.loadUrl("javascript:waveBack()");
					}
					i++;
				}
			});
		}
	}
	[Tags]/**
	 [Tags]* Provides a hook for calling "alert" from javascript. Useful for debugging
	 [Tags]* your javascript.
	 [Tags]*/
	final class MyWebChromeClient extends WebChromeClient {
		@Override
		public boolean onJsAlert(WebView view, String url, String message,
				JsResult result) {
			Log.d(LOG_TAG, message);
			result.confirm();
			return true;
		}
	}
}
```
main.xml文件: 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/mCallJS"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="CallJs" />
    <WebView
        android:id="@+id/webview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```
demo.html: 
```  
<html>
<script language="javascript">
/* This function is invoked by the activity */
function wave() {
	alert("1");
	document.getElementById("droid").src="android_waving.png";
	alert("2");
}
/* This function is invoked by the activity */
function waveBack() {
	alert("1");
	document.getElementById("droid").src="android_normal.png";
	alert("2");
}
</script>
<body>
<!-- Calls into the javascript interface for the activity -->
<a>
	<div style="width:80px;
		margin:0px auto;
		padding:10px;
		text-align:center;
		border:2px solid 202020;" >
		<img id="droid" src="android_normal.png"/><br>
			Click me!
	</div>
</a>
</body>
</html>
```