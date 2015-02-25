为了方便网页和Android应用的交互，Android系统提供了WebView中JavaScript网页脚本调用Java类方法的机制。只要调用addJavascriptInterface方法即可映射一个Java对象到JavaScript对象上。
1、映射Java对象到JavaScript对象上
```  
mWebView = (WebView) findViewById(R.id.wv_content);
mWebView.setVerticalScrollbarOverlay(true);
final WebSettings settings = mWebView.getSettings();
settings.setSupportZoom(true);
//WebView启用Javascript脚本执行
settings.setJavaScriptEnabled(true);
settings.setJavaScriptCanOpenWindowsAutomatically(true);
//映射Java对象到一个名为”js2java“的Javascript对象上
//JavaScript中可以通过"window.js2java"来调用Java对象的方法
mWebView.addJavascriptInterface(new JSInvokeClass(), "HTMLOUT");
[Tags]/**网页Javascript调用接口**/
class JSInvokeClass {
    public void back() {
        activity.finish();
    }
}
```
2、JavaScript调用Java对象示例
这里才是最关键的地方所在了，下面是通过javascript来调用java中的方法，也就是JSInvokeClass中的back方法，
```  
window.HTMLOUT.back();
```
但是上面的方法放在哪里呢？
如何去执行喃？
请看：
```  
mWebView.loadUrl("javascript:window.HTMLOUT.back();");
```
当然如果想传参数怎么办？
不要急，首先在JSInvokeClass.back方法处，申明一个代参数的方法就行了：
```  
[Tags]/**网页Javascript调用接口**/
class JSInvokeClass {
    public void back() {
        activity.finish();
    }
   public void showHtml(String html)
  {
    Log.e("Html:"+html);
  }
}
```
然后通过 javascript调用就行了，
```  
mWebView.loadUrl("javascript:window.HTMLOUT.showHtml(document.documentElement.innerHTML);");
```