我们怎么样才能做到利用缓存来读取那，大致的一共分为五步。只要是这五步要是都做好的话，那就没有问题了，下面就是讲解了，希望大家仔细的看看。
第一步第一步:新建一个Android工程命名为WebViewCacheDemo.目录结构如下:
第二步:在assets目录下新建一个html文件，命名为index.html。
第三步:修改main.xml布局文件一个WebView控件一个Button(点击加载缓存图片用)。
```  
<?xml version= "1.0" encoding= "utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <WebView
        android:id="@+id/webview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <Button
        android:id="@+id/button"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="从缓存里读取图片" />
</LinearLayout>
```
第四步:修改主核心程序EOE.android.java，这里我只加载了index.html文件，按钮事件暂时没写,代码如下:
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.webkit.WebView;
import android.widget.Button;
public class WebViewCacheDemo extends Activity {
	private WebView mWebView;
	// private Button mButton;
	private static final String url = "file:///android_asset/index.html";
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mWebView = (WebView) findViewById(R.id.webview);
		mWebView.loadUrl(url);
		// mButton = (Button)findViewById(R.id.button);
		// mButton.setOnClickListener(listener);
	}
}
```
第五步:在AndroidMainifest.xml文件中加访问网络的权限:
```  
<uses-permission android:name="android.permission.INTERNET" />
```