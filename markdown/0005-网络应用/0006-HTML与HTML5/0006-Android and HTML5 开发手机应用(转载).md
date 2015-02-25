作为一个WEB开发者，HTML5让我兴奋，因为它可以将桌面应用程序功能带入浏览器中。但在国内，看着到处横行的IE8版本以下的浏览器，觉得到能大规模使用HTML5技术的那天，还遥遥无期。但面对iOS及Android等平台的手机用户越来越多，基于Webkit内核的移动浏览器一定能让HTML5先大规模应用起来。这将对对移动Web应用程序开发具有重大影响。
作为非常看好未来手机网络的我，也在一直研究Android平台的应用的开发，也许是因为自己更熟悉HTML及CSS、JS，并受到之前使用HTML和VC开发程序的影响，我也更愿意使用HTML来做Android程序的UI….
09年，在开发《华夏风云》游戏的时候，使用了基于Google Gear插件来做了很多离线应用，可惜Gear已经不在更新开发，被HTML5取代。下面介绍基于HTML 5的Web 应用程序的本地存储，不再废话，例子说明一切。
一、离线应用缓存 HTML 5 Offline Application Cache
在服务器上添加MIME TYPE支:text/cache-manifest
如果在Apache下添加：
AddType text/cache-manifest manifest
如果为Nginx，在添加：
text/cache-manifest                   manifest;
或者通过动态程序生成：
1 header('Content-type: text/cache-manifest; charset=UTF-8');
?创建 NAME.manifest:
新建清单文件 manifest
```  
CACHE MANIFEST
 This is a comment.
 Cache manifest version 0.0.1
 If you change the version number in this comment,
 the cache manifest is no longer byte-for-byte
 identical.
/app/static/default/js/models/prototype.js
/app/static/default/js/controllers/init.js
NETWORK:
 All URLs that start with the following lines
 are whitelisted.
http://a.com/
CACHE:
 Additional items to cache.
/app/static/default/images/main/bg.png
FALLBACK:
demoimages/ images/
```
给 <html> 标签加 manifest 属性：
建立manifest文件之后，需要在HTML文档中声明：
声明清单文件 manifest
```  
<!doctype html> 
<html manifest="notebook.manifest"> 
    <head> 
        <meta charset="UTF-8" /> 
            <meta name = "viewport" content = "width = device-width, user-scalable = no"> 
        <title>NoteBook</title> 
    </head> 
    <body> 
    </body> 
</html>
```
二、Key-Value Storage
三、Using the JavaScript Database
四、Android下使用WebView来做基于HTML5的App
见如下AndroidManifest.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android
    package="com.xinze.joke
    android:versionCode="1
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".Joke"
            android:configChanges="orientation|keyboardHidden|navigation"
            android:label="@string/app_name"
            android:theme="@android:style/Theme.NoTitleBar" >
            <intent filter="" >
                <action android:name="android.intent.action.MAIN" >
                    <category android:name="android.intent.category.LAUNCHER" >
                    </category>
                </action>
            </intent>
        </activity>
    </application>
    <uses
        android:name="android.permission.INTERNET"
        permission="" >
    </uses>
</manifest>
```
注意： 
<uses android:name="android.permission.INTERNET" -permission=""></uses>，允许网络应用，必须！！
Android主程序代码：
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.app.AlertDialog.Builder;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.KeyEvent;
import android.view.View;
import android.webkit.JsPromptResult;
import android.webkit.JsResult;
import android.webkit.WebChromeClient;
import android.webkit.WebStorage;
import android.webkit.WebView;
import android.webkit.WebViewClient;
public class Joke extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		final WebView wv = new WebView(this);
		// 覆盖默认后退按钮的作用，替换成WebView里的查看历史页面
		wv.setOnKeyListener(new View.OnKeyListener() {
			@Override
			public boolean onKey(View v, int keyCode, KeyEvent event) {
				if (event.getAction() == KeyEvent.ACTION_DOWN) {
					if ((keyCode == KeyEvent.KEYCODE_BACK) && wv.canGoBack()) {
						wv.goBack();
						return true;
					}
				}
				return false;
			}
		});
		// 设置支持Javascript
		wv.getSettings().setJavaScriptEnabled(true);
		wv.getSettings().setJavaScriptCanOpenWindowsAutomatically(true);
		wv.getSettings().setDatabaseEnabled(true);
		wv.getSettings().setDatabasePath("/data/data/com.xinze.joke/databases");
		// 创建WebViewClient对象
		WebViewClient wvc = new WebViewClient() {
			@Override
			public boolean shouldOverrideUrlLoading(WebView view, String url) {
				wv.loadUrl(url);
				// 记得消耗掉这个事件。给不知道的朋友再解释一下，Android中返回True的意思就是到此为止吧,事件就会不会冒泡传递了，我们称之为消耗掉
				return true;
			}
		};
		// 设置WebViewClient对象
		wv.setWebViewClient(wvc);
		// 创建WebViewChromeClient
		WebChromeClient wvcc = new WebChromeClient() {
			// 处理Alert事件
			@Override
			public boolean onJsAlert(WebView view, String url, String message,
					final JsResult result) {
				// 构建一个Builder来显示网页中的alert对话框
				Builder builder = new Builder(Joke.this);
				builder.setTitle("笑死不偿命");
				builder.setMessage(message);
				builder.setPositiveButton(android.R.string.ok,
						new AlertDialog.OnClickListener() {
							@Override
							public void onClick(DialogInterface dialog,
									int which) {
								result.confirm();
							}
						});
				builder.setCancelable(false);
				builder.create();
				builder.show();
				return true;
			}
			// 处理Confirm事件
			@Override
			public boolean onJsConfirm(WebView view, String url,
					String message, final JsResult result) {
				Builder builder = new Builder(Joke.this);
				builder.setTitle("删除确认");
				builder.setMessage(message);
				builder.setPositiveButton(android.R.string.ok,
						new AlertDialog.OnClickListener() {
							@Override
							public void onClick(DialogInterface dialog,
									int which) {
								result.confirm();
							}

						});
				builder.setNeutralButton(android.R.string.cancel,
						new AlertDialog.OnClickListener() {
							@Override
							public void onClick(DialogInterface dialog,
									int which) {
								result.cancel();
							}
						});
				builder.setCancelable(false);
				builder.create();
				builder.show();
				return true;
			}
			// 处理提示事件
			@Override
			public boolean onJsPrompt(WebView view, String url, String message,
					String defaultValue, JsPromptResult result) {
				// 看看默认的效果
				return super.onJsPrompt(view, url, message, defaultValue,
						result);
			}
			@Override
			public void onExceededDatabaseQuota(String url,
					String databaseIdentifier, long currentQuota,
					long estimatedSize, long totalUsedQuota,
					WebStorage.QuotaUpdater quotaUpdater) {
				quotaUpdater.updateQuota(204801);
			}
		};
		wv.loadUrl("http://192.168.1.14/index.html");
		// 设置setWebChromeClient对象
		wv.setWebChromeClient(wvcc);
		setContentView(wv);
	}
}
```
使用 JavaScript Database 的时候，需要特别注意：setDatabaseEnabled 以及 onExceededDatabaseQuota！