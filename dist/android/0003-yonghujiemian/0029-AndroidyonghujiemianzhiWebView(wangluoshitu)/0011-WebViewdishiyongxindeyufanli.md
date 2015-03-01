最近一个哥们在弄新闻客户端，看起来不是很难，使用一个ListView，然后点击之后进入一个界面里面，在里面显示资讯，这个做起来看似不难，其实还是有点麻烦的！尤其是解析数据，一般情况下我们解析网页中的时间的时候，是通过Pull解析的，但是这个哥们使用SAX解析，虽然说也是可以的，但是最好使用Pull,Pull是专门对移动设备的解析使用的！方便开发者的使用！以后记住了哦！
其中要大家注意的一个问题：
注意解析文本和图片，那个图片如果你直接把图片加载进去，有可能不行，这个需要进一步调试，尤其是字数的方面，你如果不设置字数的宽度的话那么你的这个页面是很大的，可以左右拖动，但是这样体验就不好了！
如果你想知道怎么做的话，最好的办法反解码！记得有一个哥们之前写过，你们可以看看！
有可能的话，过段时间我给你们再发一个反解码的东西，全部集成，不会的话，看着里面的一个txt文档做就可以了！只需要借个步骤而已！
文档中对于WebView的解释：
WebView是进行web网页显示的，我们使用这个类为基础进行开发推出自己的Web浏览器，或者我们可以直接在当前的Activity中显示在线的内容。
WebView使用WebKit进行渲染来显示网页，通过于此，我们可以进行实现网页后退，前进，放大，缩小或者搜索或者更多功能；
下面看看WebView的使用吧：
【注意】使用WebView,，因为用处到了网络，所以我们必须在AndroidManifset.xml文件中进行权限设置
接下来去实现WebView，需要下面一些步骤
一：要在布局文件那边声明WebView组件
二：在Activity中进行实例化
三：调用WebView的loadUrl()方法来实现。加载指定的URL地址的网页
Demo源代码：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;
import android.webkit.WebView;
public class WebView_Test extends Activity {
	private WebView webView;
	private static final String URL = "http://www.google.com";
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 取消标题
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		// 进行全屏
		this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		setContentView(R.layout.webview);
		// 实例化WebView
		webView = (WebView) this.findViewById(R.id.wv_oauth);
		 /**
		  * 调用loadUrl()方法进行加载内容
		  */
		webView.loadUrl(URL);
		 /**
		  * 设置WebView的属性，此时可以去执行JavaScript脚本
		  */
		webView.getSettings().setJavaScriptEnabled(true);
	}
}
```
XML文件的定义：
```  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="fill_parent"
	android:layout_height="fill_parent"
	android:orientation="vertical" >
</LinearLayout>
```
效果截图：
![img](P)  
①：有时候我们我们需要WebView能够实现超链接的功能。我们可以调用setWebViewClient()方法试着WebView的客户端，
此时我们只要重写一下WebViewClient类下的public boolean shouldOverrideKeyEvent (WebView view, KeyEvent event)就可以了
源代码如下：
```  
private class myWebViewClient extends WebViewClient {
	@Override
	public boolean shouldOverrideKeyEvent(WebView view, KeyEvent event) {
		webView.loadUrl(URL);
		return true;
	}
}
```
②：考虑到网页的加载速度，我们可以调用setWebChromeClient()方法
我们此时只要重写一下WebChromeClient类中的
public void onProgressChanged (WebView view, int newProgress)来显示页面的加载进度,实例代码如下：
```  
webview.setWebChromeClient(new WebChromeClient() {
	@Override
	public void onProgressChanged(WebView view, int newProgress) {
		if (newProgress == 100) {
			handler.sendEmptyMessage(CLOSE_DIA);
		}
		super.onProgressChanged(view, newProgress);
	}
});
```
在代码里面可以使用handle，如果加载的进度是100，发出消息让handler,进行处理！