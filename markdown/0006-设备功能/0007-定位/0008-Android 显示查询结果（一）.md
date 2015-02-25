在Google地图上显示查询结果（1）
上一节中，服务器端程序已经可以正常返回查询结果。本节将介绍如何从服务器取得该结果并显示在地图上。这一过程主要分为三个步骤：第一，需要获取服务器端的查询结果；第二，实现loadGeoInfo()接口；第三，显示查询结果等其他工作。
1．获取服务器端的查询结果
本程序将使用流行的AJAX技术取得查询结果，一方面减少了流量，另一方面由于加载速度快，还可以增强用户体验。当然Google Maps API在这里也提供了相当便捷的使用AJAX的方法，完全没有必要使用其他AJAX的应用程序框架。在Google Maps API中，异步调用有两种方法，一种是使用GXmlHttp对象，另一种是使用GDownloadUrl()函数。
（1）使用GXmlHttp对象
创建GXmlHttp对象使用AJAX和直接创建XmlHttpRequest对象基本没有区别，使用十分灵活。由于Google封装时已经考虑到了浏览器的兼容性，所以GXmlHttp相对普通XmlHttpRequest的优势在于可以直接在不同浏览器上直接使用。目前GXmlHttp不仅可以支持Internet Explorer、Firefox和Opera等流行的浏览器，在Safari、Konquorer等用户群比较少的浏览器上都可以得到比较好的支持。下面仅给出一段模版形式的代码以供参考。
```  
//创建GXmlHttp对象 
var request = GXmlHttp.create();
//打开GXmlHttp，这里可以设置的参数有三个 
//第一个参数：获取方法，判断是使用GET方法还是POST方法 
//第二个参数：需获取的文件名 
//第三个参数：获取模式，异步为真，同步为假 
request.open("GET", "myfile.txt", true);
//回调函数，可以用function(){…}直接在此调用 
//也可以预先定义函数XXX()，在此赋值 
request.onreadystatechange=XXX request.onreadystatechange = function() {
	//判断状态，可根据不同的状态做不同的响应，本例中只捕捉了完全加载的状态4 
	if (request.readyState == 4) { 
		alert(request.responseText);
	} 
} 
//发送信息 
request.send(null);
```
（2）使用GDownloadUrl()函数
GDownloadUrl()函数应该说是一个简化版的异步处理函数，只能使用GET方法，不判断加载状态，只是在完全加载后调用回调函数。虽然GDownloadUrl()函数功能有限，但是因为其使用非常简单，所以应用也相当广泛。GDownloadUrl()调用方法如下。
```  
GDownloadUrl(url, onload)
```
第一个参数即为需要用GET方法获取的URL，第二个参数onload是完全加载后的回调函数。
在本例IP地理位置可视化查询中，GDownloadUrl()已经完全符合要求，故在此使用GDownloadUrl()函数。获取服务器数据的函数getGeoInfo()如下。
```  
function getGeoInfo(q) {
	//q为待查信息 
	GDownloadUrl("search.php?q="+q, function(data) {
		//直接用eval执行返回的Javascript字符串 
		eval(data);
	}); 
}
```
2．实现loadGeoInfo()接口
loadGeoInfo()接口需要实现在Google地图上定位目标IP，添加信息窗口显示其详细信息等功能。
```  
function loadGeoInfo(q, ip, country_code, country_code3, country, region, city, latitude, longitude) {
	//新的信息窗口中的内容 
	var info = "<div align=\"left\" style=\"overflow:X;font-size:12px\">"
			+ "<span style=\"font-size:14px\"><strong>" + q
			+ "</strong></span><br />" + "<strong>IP:</strong>  " + ip
			+ "<br />" + "<strong>国家:</strong>  " + country + "<br />"
			+ "<strong>代码:</strong>  " + country_code + "(" + country_code3 + ")"
			+ "<br />" + "<strong>省份:</strong>  " + city + "<br />" + "<strong>城市:</strong>  "
			+ region + "<br />" + "<strong>经度:</strong>  " + longitude + "<br />"
			+ "<strong>纬度:</strong>  " + latitude + "<br />" + "</div>";
	//移动地图中心到新的位置 
	var point = new GLatLng(latitude, longitude);
	map.panTo(point);
	//如果创建了marker地标，则关闭当前的信息窗口并移除地标 
	if(marker) {
		map.closeInfoWindow();
		map.removeOverlay(marker);
	} 
	//创建新的地标 
	marker = new GMarker(point);
	map.addOverlay(marker);
	//显示信息窗口 
	marker.openInfoWindowHtml(info);
}
```