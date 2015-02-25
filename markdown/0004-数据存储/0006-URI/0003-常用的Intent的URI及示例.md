以下是常用到的Intent的URI及其示例，包含了大部分应用中用到的共用Intent。
一、打开一个网页，类别是Intent.ACTION_VIEW
```  
Uri uri = Uri.parse("http://blog.3gstdy.com/");
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
```
二、打开地图并定位到一个点
```  
Uri uri = Uri.parse("geo:52.76,-79.0342″);
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
```
 三、打开拨号界面 ,类型是Intent.ACTION_DIAL
```  
Uri uri = Uri.parse("tel:10086″);
Intent intent = new Intent(Intent.ACTION_DIAL, uri);
```
四、直接拨打电话,与三不同的是，这个直接拨打电话，而不是打开拨号界面
```  
Uri uri = Uri.parse("tel:10086″);
Intent intent = new Intent(Intent.ACTION_CALL, uri);
```
五、卸载一个应用，Intent的类别是Intent.ACTION_DELETE
```  
Uri uri = Uri.fromParts("package", "xxx", null);
Intent intent = new Intent(Intent.ACTION_DELETE, uri);
```
六、安装应用程序,Intent的类别是Intent.ACTION_PACKAGE_ADDED
```  
Uri uri = Uri.fromParts("package", "xxx", null);
Intent intent = new Intent(Intent.ACTION_PACKAGE_ADDED, uri);
```
七、播放音频文件
```  
Uri uri = Uri.parse("file:///sdcard/download/everything.mp3″);
Intent intent = new Intent(Intent.ACTION_VIEW, uri);
intent.setType("audio/mp3″);
```
八、打开发邮件界面
```  
Uri uri= Uri.parse("mailto:admin@3gstdy.com");
Intent intent = new Intent(Intent.ACTION_SENDTO, uri);
```
九、发邮件,与八不同这里是将邮件发送出去，
```  
Intent intent = new Intent(Intent.ACTION_SEND);
String[] tos = { "admin@3gstdy.com" };
String[] ccs = { "webmaster@3gstdy.com" };
intent.putExtra(Intent.EXTRA_EMAIL, tos);
intent.putExtra(Intent.EXTRA_CC, ccs);
intent.putExtra(Intent.EXTRA_TEXT, "I come from http://blog.3gstdy.com");
intent.putExtra(Intent.EXTRA_SUBJECT, "http://blog.3gstdy.com");intent.setType("message/rfc882″);
Intent.createChooser(intent, "Choose Email Client")
```
十、路径规划
```  
Uri uri = Uri.parse("http://maps.google.com/maps?f=d&saddr=startLat%20startLng&daddr=endLat%20endLng&hl=en");
Intent it = new Intent(Intent.ACTION_VIEW, uri);
startActivity(it);
```
