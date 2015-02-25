前几天项目遇到了webview加载HTML的时候，网页拖动右边有空白的现象，找了很多方案，都没解决，我研究了一下，其实很简单，特把方法列出来，供大家参考一下，希望能对大家有所帮助。
先看效果图
怎么拖都不会动，同时加 有对网页放大和缩小的功能。
![img](P)  
![img](P)  
先把代码拿出来，HTML的我是PHP写的，
```  
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	<title><? echo $arr['subject'];?></title>
	<style type="text/css">
		<!--
		[Tags]*{ margin:0; padding:0}
		body{
			margin-left:0;
			margin-right:0;
		}
		.STYLE1 {color: 006699; font-weight:bold}
		-->
	</style>
</head>
<body>
<table width="98%" border="0" cellspacing="2" cellpadding="2">
  <tr>
    <td ><span class="STYLE1"><? echo $arr['subject'];?></span></td>
  </tr>
  <tr>
    <td >作者:<? echo $arr[author];?>  时间:<? echo tranTime($arr[postdate]);?></td>
  </tr>
  <tr>
    <td><? echo $content;?></td>
  </tr>
</table>
</body>
</html>
```
跟一般的写法没什么两样，其中上面加载PHP的我省略掉了，相信大家能看明白。
下面是JAVA代码：
```  
String url="http://www.dengwei1999.com/newslist.php?id=8";
wv = (WebView) findViewById(R.id.webView1);
wv.setVisibility(WebView.VISIBLE);
WebSettings ws = wv.getSettings();
//ws.setUseWideViewPort(true);
ws.setJavaScriptEnabled(true);
wv.addJavascriptInterface(new ContactsPlugin(), "contactsAction");
//设置可以支持缩放
wv.getSettings().setSupportZoom(true);   
//设置默认缩放方式尺寸是far  
wv.getSettings().setDefaultZoom(WebSettings.ZoomDensity.FAR);  
//设置出现缩放工具   
wv.getSettings().setBuiltInZoomControls(true);
wv.loadUrl(url);
```
就这样就可以实现了，是不是很简单。