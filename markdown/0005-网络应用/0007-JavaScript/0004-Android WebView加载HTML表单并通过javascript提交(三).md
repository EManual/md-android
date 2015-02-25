java代码：
```  
public class WebViewTest extends Activity {
	private WebView mWebView = null;
	private TextView txtView = null;
	private Handler mHandler = new Handler();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mWebView = (WebView) findViewById(R.id.webView);
		txtView = (TextView) findViewById(R.id.webViewResult);
		WebSettings webSettings = mWebView.getSettings();
		// 不保存密码
		webSettings.setSavePassword(false);
		// 不保存表单数据
		webSettings.setSaveFormData(false);
		webSettings.setJavaScriptEnabled(true);
		// 不支持页面放大功能
		webSettings.setSupportZoom(false);
		mWebView.addJavascriptInterface(new LoginJavaScriptImpl(), "loginImpl");
		mWebView.setWebChromeClient(new MyAndroidWebClient());
		((Button) findViewById(R.id.btnLoadhtml))
				.setOnClickListener(new View.OnClickListener() {
					public void onClick(View arg0) {
						mWebView.loadData(createWebForm(), "text/html", "UTF-8");
						// mWebView.loadDataWithBaseURL("", createWebForm(),
						// "text/html", "UTF-8", "");
					}
				});
	}
	private String returnValue;
	protected final class LoginJavaScriptImpl {
		public void login(String username, String password) {
			returnValue = username + ": " + password;
			mHandler.post(new Runnable() {
				public void run() {
					txtView.setText(returnValue);
				}
			});
		}
	}
	private final class MyAndroidWebClient extends WebChromeClient {
		@Override
		public boolean onJsAlert(WebView view, String url, String message,
				JsResult result) {
			new AlertDialog.Builder(WebViewTest.this)
					.setTitle("提示信息")
					.setMessage(message)
					.setPositiveButton("确定",
							new DialogInterface.OnClickListener() {
								public void onClick(
										DialogInterface dialoginterface, int i) {
								}
							}).show();
			return true;
		}
	}
}
```