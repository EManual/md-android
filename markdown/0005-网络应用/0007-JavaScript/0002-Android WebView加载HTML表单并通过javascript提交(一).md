使用android的WebView控件加载HTML表单，通过javascript调用java对象提交表单，在java对象中获取表单的值：
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.os.Handler;
import android.view.View;
import android.webkit.JsResult;
import android.webkit.WebChromeClient;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.widget.Button;
import android.widget.TextView;
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
		}
	}
}
```