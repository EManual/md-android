Android中如何显示本地HTML：
```  
// one
webview = (WebView) findViewById(R.id.webview);
webview.getSettings().setJavaScriptEnabled(true);
webview.loadUrl("content://com.android.htmlfileprovider/sdcard/index.html");
// two
Uri uri = Uri.parse("content://com.android.htmlfileprovider/sdcard/01.htm");
Intent intent = new Intent();
intent.setData(uri);
intent.setClassName("com.android.htmlviewer","com.android.htmlviewer.HTMLViewerActivity");
startActivity(intent);
// three
String encoding = "UTF-8";
String mimeType = "text/html";
final String html = "锟斤拷锟斤拷google" + "锟斤拷锟斤拷google";
mWebView.loadDataWithBaseURL("file://", html, mimeType, encoding, "about:blank");
```
Android开发：TextView中显示HTML和图片
最近有人咨询我如何在TextView中显示 html标签内的图片，大家都知道，在TextView中显示HTML内容的方法如下所示：
1 TextView description=(TextView)findViewById(R.id.description);
2 description.setText(Html.fromHtml(item.getDescription()));
如果HTML中有图片的话，显示出来的图片会被一个小框取代，那么怎么样才能看到图片呢？查看了一下API，android.text.Html还还有 另一个方法：Html.fromHtml(String source,ImageGetter imageGetter,TagHandler tagHandler)，这个方法使用如下所示：
```  
ImageGetter imgGetter = new Html.ImageGetter() {
	public Drawable getDrawable(String source) {
		Drawable drawable = null;
		Log.d("Image Path", source);
		URL url;
		try {
			url = new URL(source);
			drawable = Drawable.createFromStream(url.openStream(), "");
		} catch (Exception e) {
			return null;
		}
		drawable.setBounds(0, 0, drawable.getIntrinsicWidth(),
				drawable.getIntrinsicHeight());
		return drawable;
	}
};
TextView description = (TextView) findViewById(R.id.description);
description.setText(Html.fromHtml(item.getDescription(), imgGetter, null));
```