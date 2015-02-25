Intent应该算是Android中特有的东西。你可以在Intent中指定程序要执行的动作（比如：view,edit,dial），以及程序执行到该动作时所需要的资料。都指定好后，只要调用startActivity()，Android系统会自动寻找最符合你指定要求的应用程序，并执行该程序。
下面列出几种Intent的用法
显示网页:
```  
Uri uri = Uri.parse("http://www.google.com");
Intent it  = new Intent(Intent.ACTION_VIEW,uri);
startActivity(it);
```
显示地图:
```  
Uri uri = Uri.parse("geo:38.899533,-77.036476");
Intent it = new Intent(Intent.Action_VIEW,uri);
startActivity(it);
```
路径规划:
```  
Uri uri = Uri.parse("http://maps.google.com/maps?f=d&saddr=startLat%20startLng&daddr=endLat%20endLng&hl=en");
Intent it = new Intent(Intent.ACTION_VIEW,URI);
startActivity(it);
```
拨打电话:
调用拨号程序
```  
Uri uri = Uri.parse("tel:xxxxxx");
Intent it = new Intent(Intent.ACTION_DIAL, uri);  
startActivity(it);  
Uri uri = Uri.parse("tel.xxxxxx");
Intent it =new Intent(Intent.ACTION_CALL,uri);
```
要使用这个必须在配置文件中加入<uses-permission id="android.permission.CALL_PHONE" />
发送SMS/MMS
调用发送短信的程序
```  
Intent it = new Intent(Intent.ACTION_VIEW);   
it.putExtra("sms_body", "The SMS text");   
it.setType("vnd.android-dir/mms-sms");   
startActivity(it);  
```
发送短信
```  
Uri uri = Uri.parse("smsto:0800000123");   
Intent it = new Intent(Intent.ACTION_SENDTO, uri);   
it.putExtra("sms_body", "The SMS text");   
startActivity(it); 
```
发送彩信
```  
Uri uri = Uri.parse("content://media/external/images/media/23");   
Intent it = new Intent(Intent.ACTION_SEND);   
it.putExtra("sms_body", "some text");   
it.putExtra(Intent.EXTRA_STREAM, uri);   
it.setType("image/png");   
startActivity(it);
```
发送Email
```  
Uri uri = Uri.parse("mailto:xxx@abc.com");
Intent it = new Intent(Intent.ACTION_SENDTO, uri);
startActivity(it);
Intent it = new Intent(Intent.ACTION_SEND);   
it.putExtra(Intent.EXTRA_EMAIL, "me@abc.com");   
it.putExtra(Intent.EXTRA_TEXT, "The email body text");   
it.setType("text/plain");   
startActivity(Intent.createChooser(it, "Choose Email Client"));  
Intent it=new Intent(Intent.ACTION_SEND);     
String[] tos={"me@abc.com"};     
String[] ccs={"you@abc.com"};     
it.putExtra(Intent.EXTRA_EMAIL, tos);     
it.putExtra(Intent.EXTRA_CC, ccs);     
it.putExtra(Intent.EXTRA_TEXT, "The email body text");     
it.putExtra(Intent.EXTRA_SUBJECT, "The email subject text");     
it.setType("message/rfc822");     
startActivity(Intent.createChooser(it, "Choose Email Client"));
```
添加附件
```  
Intent it = new Intent(Intent.ACTION_SEND);   
it.putExtra(Intent.EXTRA_SUBJECT, "The email subject text");   
it.putExtra(Intent.EXTRA_STREAM, "file:///sdcard/mysong.mp3");   
sendIntent.setType("audio/mp3");   
startActivity(Intent.createChooser(it, "Choose Email Client"));
```
播放多媒体
```  
Intent it = new Intent(Intent.ACTION_VIEW);
Uri uri = Uri.parse("file:///sdcard/song.mp3");
it.setDataAndType(uri, "audio/mp3");
startActivity(it);
Uri uri = Uri.withAppendedPath(MediaStore.Audio.Media.INTERNAL_CONTENT_URI, "1");   
Intent it = new Intent(Intent.ACTION_VIEW, uri);   
startActivity(it);  
```
Uninstall 程序
```  
Uri uri = Uri.fromParts("package", strPackageName, null);   
Intent it = new Intent(Intent.ACTION_DELETE, uri);   
startActivity(it);
```