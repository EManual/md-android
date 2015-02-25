最后为网页设计好LOGO，添加相应的事件响应（如搜索按钮单击，搜索栏回车等）就可以使用了。完整的首页index.html代码如下。
```  
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "/TR/xhtml1/DTD/xhtml1-strict.dtd">
<head> 
<meta http-equiv="content-type" content="text/html; charset=utf-8"/> 
<title>GeoIP 搜索者</title> 
<!--导入Google Maps API库文件。注意将本代码中的API Key替换为前文申请到的API Key--> 
<script src="http://maps.google.com/maps?file=api&v=2&key=ABQIAAAA1- j86tnUDFv8OAt C8dZVtKRT2yXp_ZAY8_ufC3CFXhHIE1NvwkxSzmwrQ90SNUILzGRpsBiaa860gfQ" type="text/javascript">
</script> 
<script type="text/javascript"> //<![CDATA[ var map; //全局GMap2对象 var marker; 
//用于标识查询IP的GMarker地标 
//初始化 
function load() { 
	if (GBrowserIsCompatible()) { 
		map = new GMap2(document.getElementById("map"));
		map.setCenter(new GLatLng(39.92, 116.46), 2);
		//添加相应GControl()控件 
		map.addControl(new GSmallMapControl());
		map.addControl(new GMapTypeControl());
		//设定地图类型为混合地图 
		map.setMapType(G_HYBRID_MAP);
		//查询当前访客信息 
		getGeoInfo("");
	} 
} 
//响应查询栏的回车 
//因为FireFox中event不是全局的，所以必须从相应DOM对象里传回event 
//如下写法可兼容IE和Firefox function pressEnter(event, q) { 
//如果输入了回车，则执行查询 
if(event.keyCode==13 || event.keyCode==10) { getGeoInfo(q); } 
//查询函数 
function getGeoInfo(q) { 
	GDownloadUrl("search.php?q="+q, function(data) {
		eval(data); });
} 
//服务器端数据调用接口 
function loadGeoInfo(q, ip, country_code, country_code3, country, region, city, latitude, longitude) { //新的信息窗口中的内容 
	var info = "<div align=\"left\" style=\"overflow:X; font-size:12px\">" + "<span style=\"font-size:14px\"><strong>" + q + "</strong></span><br />" + "<strong>ＩＰ:</strong>  " + ip + "<br />" + "<strong>国家:</strong>  " + country + "<br />" + "<strong>代码:</strong>  " + country_code + "(" + country_code3 + ")" + "<br />" + "<strong>省份:</strong>  " + city + "<br />" + "<strong>城市:</strong>  " + region + "<br />" + "<strong>经度:</strong>  " + longitude + "<br />" + "<strong>纬度:</strong>  " + latitude + "<br />" + "</div>";
	//移动地图中心到新的位置 
	var point = new GLatLng(latitude, longitude);
	map.panTo(point);
	//如果创建了marker地标，则关闭当前的信息窗口并移除地标 
	if(marker) { map.closeInfoWindow(); map.removeOverlay(marker); }
	//创建新的地标 
	marker = new GMarker(point); map.addOverlay(marker);
	//显示信息窗口 
	marker.openInfoWindowHtml(info);
} 
//]]> 
</script> 
<style> 
	td{ text-align:center; }
</style> 
</head> 
<body onload="load()" onunload="GUnload()"> 
<table cellSpacing="0" cellPadding="0" width="600" border="0" align="center"> 
<tbody> 
<tr> 
	<td>
	<img src="geoipseeker.jpg" title="geoipseeker" alt="geoipseeker" width="450" height="50" style="boder:0" />
	<td>
</tr> 
<tr>
	<td height="25">
	<!--此处onsubmit为"return false;"可防止表单提交，因为本例中用AJAX查询，无须提交表单-->
	<form onsubmit="return false;">
		<!--分别为输入框和按钮都添加了事件监听。回车和点击按钮都可以进行查询-->
		<label for="q">在此输入IP或域名<input maxLength="50" size="25" name="q" id="q" onkeypress="pressEnter(event, this.value); " />
			<input type="button" value="查找" id="search" onclick="getGeoInfo(q. value)" />
		</label>
	</form>
	</td>
</tr> 
<tr> 
	<td height="20" id="info"></td>
</tr> 
<tr> 
	<td>
		<div id="map" style="width:580px;height:350px"></div>
	</td>
</tr> 
<tr> 
	<td height="20" id="link">
		<a href="http://blog.gmap2.net">Power by<strong>GMap2.net</strong></a>
	</td>
</tr> 
</tbody> 
</table> 
</body> 
</html>
```