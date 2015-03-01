WebView用来显示网页。先看效果
![img](P)  
![img](P)  
#### 一、概述
它使您可以滚动自己的Web浏览器或简单地显示在您网上活动的某些内容。它采用了WebKit渲染引擎来显示网页的方法，包括向前和向后导航的历史，放大和缩小，执行文本搜索和更要启用内置的变焦。
#### 二、重要方法
```  
addJavascriptInterface(Object obj, String interfaceName)：使用此函数来绑定一个对象的Javascript，该方法可以访问JavaScript
loadData(String data, String mimeType, String encoding)：此方法经常出现乱码，尽量少用
loadDataWithBaseURL(String baseUrl, String data, String mimeType, String encoding, String historyUrl)：加载到WebView给定的数据，以此为基础内容的网址提供的网址。
capturePicture()：捕捉当前WebView的图片
clearCache(boolean includeDiskFiles)：清除资源的缓存
destroy()：销毁此WebView
```
#### 三、实例
1.布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" >
        <WebView
            android:id="@+id/wv1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
        <WebView
            android:id="@+id/wv2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
        <WebView
            android:id="@+id/wv3"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </LinearLayout>
</ScrollView>
```
2.Java代码
```  
public class WebViewDemo extends Activity {
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.webviewpage);
		final String mimeType = "text/html";
		final String encoding = "utf-8";
		WebView wv;
		wv = (WebView) findViewById(R.id.wv1);
		wv.loadDataWithBaseURL("http://www.google.com",
				"<a href='http://www.baidu.com'>百度搜索</a>", mimeType, encoding,
				"");
		wv = (WebView) findViewById(R.id.wv2);
		wv.loadDataWithBaseURL("http://www.google.com",
				"<a href='www.cnblogs.com'>博客园</a>", mimeType, encoding, "");
		// 出现乱码，因此本人介意一般情况下不要使用此方法。
		wv = (WebView) findViewById(R.id.wv3);
		wv.loadData("<a href='x'>日本女优网</a>", mimeType, encoding);
	}
}
```