我们都知道，手机时代的来临的主要标志是啥？能够方便的接入互联网！互联网展现给我们的方式一般都是网页，网页中又必不可少的拥有javascript，所以说，android提供对javascript的支持那是迫在眉睫了，幸好，android早就给我们提供了无缝连接。让我们可以通过android与javascript进行交互。
我们的应用很简单，如图：
![img](P)  
我们有一个输入框，旁边有个按钮，点击按钮就会提示我们输入的内容。当然这只是html中最简单的程序了，但是你将这个程序放入android手机中访问下试试，它是不会进行提示的。要想让其以android的形式提示用户，我们就需要用到android和javascript的交互。对了，这里展示的是一个网页哦，代码如下：
```  
<html> 
<head> 
<title>js交互android</title> 
<mce:script type="text/javascript"><!-- 
function show(){ 
	var a = document.getElementById("text").value;
	alert(a);
} 
// --></mce:script> 
</head> 
<body> 
<form action=""> 
	<input type="text" id="text" value=""/>
	<input type="button" id="button" onclick="window.chenzheng_java.show()" value="clickme"/>
</form> 
</body> 
</html> 
```
再看看我们的activity代码：
```  
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.util.Log;
import android.webkit.JsResult;
import android.webkit.WebChromeClient;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.widget.Toast;
public class JavaScriptActivity extends Activity {
	WebView webView;
	Handler handler = new Handler();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		webView = (WebView) findViewById(R.id.webView1);
		[Tags]/**
		 [Tags]* webSettings 保存着WebView中的状态信息。当WebView第一次被创建时，webSetting中
		 [Tags]* 存储的都为默认值。WebSetting和WebView是一一绑定的。如果webView被销毁了，那么
		 [Tags]* 我们再次调用webSetting中的方法时，会抛出异常。
		 [Tags]*/
		WebSettings webSettings = webView.getSettings();
		webSettings.setJavaScriptEnabled(true);
		webView.loadUrl("file:///data/js.html");
		[Tags]/***
		 [Tags]* webChromeClient是一个比较神奇的东西，其里面提供了一系列的方法，
		 [Tags]* 分别作用于我们的javascript代码调用特定方法时执行，我们一般在其内部 将javascript形式的展示切换为android的形式。
		 [Tags]* 例如：我们重写了onJsAlert方法，那么当页面中需要弹出alert窗口时，便 会执行我们的代码，按照我们的Toast的形式提示用户。
		 [Tags]*/
		class MyWebChromeClient extends WebChromeClient {
			@Override
			public boolean onJsAlert(WebView view, String url, String message,
					JsResult result) {
				Toast.makeText(getApplicationContext(), message,
						Toast.LENGTH_LONG).show();
				return true;
			}
		}
		webView.setWebChromeClient(new MyWebChromeClient());
		/*
		 [Tags]* 为javascript提供一个回调的接口，这里要注意，一定要在单独的线程中实现,要不会阻塞线程的
		 [Tags]* addJavascriptInterface(Object obj, String interfaceName)
		 [Tags]* obj代表一个java对象，这里我们一般会实现一个自己的类，类里面提供我们要提供给javascript访问的方法
		 [Tags]* interfaceName则是访问我们在obj中声明的方法时候所用到的js对象
		 [Tags]* ，调用模式为window.interfaceName.方法名()
		 [Tags]*/
		webView.addJavascriptInterface(new Object() {
			public void show() {
				handler.post(new Runnable() {
					@Override
					public void run() {
						Log.i("通知", "调用了该方法哦");
						/*
						 [Tags]* 通过webView.loadUrl("javascript:xxx")方式就可以调用当前网页中的名称
						 [Tags]* 为xxx的javascript方法
						 [Tags]*/
						webView.loadUrl("javascript:show()");
					}
				});
			}
		}, "chenzheng_java");
	}
}
```
注意：
1)为了让WebView从apk文件中加载assets，Android SDK提供了一个schema，前缀为"file:///android_asset/"。WebView遇到这样的schema，就去当前包中的assets目录中找内容。如上面的"file:///android_asset/demo.html"
2)addJavascriptInterface方法中要绑定的Java对象及方法要运行另外的线程中，不能运行在构造他的线程中，这也是使用Handler的目的。
3)如果你要访问网络，请在androidManifest.xml中加上权限
```  
<uses-permission android:name="android.permission.INTERNET"></uses-permission>
```