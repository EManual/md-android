作为下一代的网页语言，HTML5拥有很多让人期待已久的新特性。HTML5的优势之一在于能够实现跨平台游戏编码移植，现在已经有很多公司在移动设备上使用HTML5技术。随着HTML5跨平台支持的不断增强和智能手机的迅速普，HTML5技术有着非常好的发展前景，甚至有人预言HTML5将引燃移动平台游戏开发技术的新革命。
越来越多的开发者热衷于使用html5+JavaScript开发移动Web App.不过，HTML5 Web APP的出现能否在未来取代移动应用，就目前来说，还是个未知数.一方面，用户在使用习惯上，不喜欢在浏览器上输入复杂的网址；另一方面，Html5 Web App 存放在服务器端，在每次使用时需要进行数据传递，会造成流量浪费。有些开发者不想接触复杂的JAVA代码，那么，有什么办法，既可以使用HTMl5开发应用，又可以将其简单封装成APK文件呢？
#### 一、Android SDK中的WebView
1.在要Activity中实例化WebView组件：WebView webView = new WebView(this)；
2.调用WebView的loadUrl()方法，设置WevView要显示的网页：
互联网用：webView.loadUrl("http：//www.31358.com")；
本地文件用：webView.loadUrl("file：///android_asset/XX.html")； 本地文件存放在：assets 文件中
3.调用Activity的setContentView( )方法来显示网页视图
4.用WebView点链接看了很多页以后为了让WebView支持回退功能，需要覆盖覆盖Activity类的onKeyDown()方法，如果不做任何处理，点击系统回退剪键，整个浏览器会调用finish()而结束自身，而不是回退到上一页面
5.需要在AndroidManifest.xml文件中添加权限，否则会出现Web page not available错误.
```  
<uses-permission android：name="android.permission.INTERNET" />
```
缺点：如果是载入的是普通网页，没有什么问题，但如果是html5，封装后，在android2.3以上才能正常访问，android2.2及以下，SDK中的WebView还没完全支持HTML5
下面是具体例子：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.KeyEvent;
import android.webkit.WebView;
public class MainActivity extends Activity {
	private WebView webview;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 实例化WebView对象
		webview = new WebView(this);
		// 设置WebView属性，能够执行Javascript脚本
		webview.getSettings().setJavaScriptEnabled(true);
		// 加载需要显示的网页
		webview.loadUrl("http：//www.31358.cn/");
		// 设置Web视图
		setContentView(webview);
	}
	@Override
	// 设置回退
	// 覆盖Activity类的onKeyDown(int keyCoder，KeyEvent event)方法
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		if ((keyCode == KeyEvent.KEYCODE_BACK) && webview.canGoBack()) {
			webview.goBack(); // goBack()表示返回WebView的上一页面
			return true;
		}
		return false;
	}
}
```
在AndroidManifest.xml文件中添加权限
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android
    package="com.android.webview.activity
    android:versionCode="1
    android:versionName="1.0" >
    <uses-sdk android:minSdkVersion="10" />
    <application
        android:icon="@drawable/icon
        android:label="@string/app_name" >
        <activity
            android:name=".MainActivity
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```
#### 二、使用PhoneGap
PhoneGap是一个用基于HTML，CSS和JavaScript的，创建移动跨平台移动应用程序的快速开发平台.它使开发者能够利用iPhone，Android，Palm，Symbian，WP7，Bada和Blackberry智能手机的核心功能——包括地理定位，加速器，联系人，声音和振动等，此外PhoneGap拥有丰富的插件，可以以此扩展无限的功能.PhoneGap是免费的，但是它需要特定平台提供的附加软件，例如iPhone的iPhone SDK，Android的Android SDK等。
优点：在Eclipse中加入SDK，编程自由，完美适应不同设备屏幕大小，适合高手使用.
缺点：没有使用布局，直接加载网页，不能添加广告.
#### 三、使用Rexsee在线生成
Rexsee是开源的Android开发平台，支持开发者以标准化Web开发模式，使用HTML5、CSS3、 Javascript快速实现移动应用.会HTML就会Android.你要做的只是将做好的HTML5 应用上传到Rexsee服务器，很快，会编译成标准的APK安装文件.
优点：一键生成，适学普通人使用。
缺点：直接封装，无法添加广告。
#### 四、appMobi Html5 XDK 在线生成(使用了PhoneGap插件)