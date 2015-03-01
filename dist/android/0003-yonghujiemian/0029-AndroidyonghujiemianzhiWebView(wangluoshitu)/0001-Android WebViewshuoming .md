webview相当于android中的浏览器，基于webkit开发，可以浏览网页文件，支持css javascript 以及html
使用webview首先要有以下配置：
1. AndroidManifest.xml中必须注册"android.permission.INTERNET"进行权限许可,(如果只是使用本地HTML，可以不用注册许可权限)否则会出Web page not available错误。 
2.如果在web中使用js需要许可javascript执行：
```  
WebView webv =(WebView)findViewById(R.id.webv);
//从xml中获取webview
webv.getSettings().setJavaScriptEnabled(true)
//允许JS执行
```
3.如果在用webview做应用的时候我们不希望新建webview进程，让程序跳来跳去那么进行如下设置
```  
webv.setWebViewClient(new WebViewClient(){ 
	public boolean shouldOverrideUrlLoading(WebView view, String url) {
		view.loadUrl(url);
		//点击超链接的时候重新在原来进程上加载URL
		return true;
	}
});
```
4.在做webview开发是经常会加载本机的html文件如下：
```  
file:///android_asset/teste.html 加载项目assets下的文件teste.html
file:///sdcard/index.html 加载sdcard下的index.html文件
```
5.在javascript中调用java方法
5.1先将一个当前的java对象绑定到一个javascript上面，使用如下方法
```  
webv.addJavascriptInterface(this,"someThing");//this为当前对象，绑定到js的someThing上面，主要someThing的作用域是全局的。一旦初始化便可随处运行
```
5.2定义被调用的java方法
```  
import android.app.Activity;
import android.os.Bundle;
import android.webkit.WebView;
public class SDFSDFSD extends Activity {
	WebView webv;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		webv = (WebView) findViewById(R.id.webv);
		webv.getSettings().setJavaScriptEnabled(true);
		webv.addJavascriptInterface(this, "someThing");
		webv.loadUrl("file:///android_asset/index.html");
	}
	public void setSmething(String some) {
		System.out.println("----------" + some + "---------------");
	}
}
```
html代码：
```  
<html>
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
	<title></title>
	<script language="javascript" type="html/text"> 
		function dosomething(){
			document.getElementById("helloweb").innerHTML="HelloWebView";
		}
	</script>
</head>
<body onload="javascript:window.someThing.setSmething('HelloWebView')">
	<div id="helloweb">
	</div>
</body>
</html>
```