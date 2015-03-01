JavaScript判断方法
搜索user agent字符串中的Android单词是最省事儿的方法：
```  
if(navigator.userAgent.match(/Android/i)) { 
	// Do something! 
	// Redirect to Android-site? 
	window.location = 'http://android.davidwalsh.name';
}
```
PHP判断方法
同样，我们可以在PHP中使用strstr方法搜索user agent中是否有Android：
```  
if(strstr($_SERVER['HTTP_USER_AGENT'],'Android')) { 
	header('Location:http://android.davidwalsh.name );
	exit();
}
```
另外，可以通过.htaccess来判断
我们可以使用.htaccess来判断和响应安卓设备!
```  
RewriteCond %{HTTP_USER_AGENT} ^.*Android.*$ 
RewriteRule ^(.*)$ http://android.davidwalsh.name  
```