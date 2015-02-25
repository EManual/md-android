Intent在Email上的使用
通过自定义Intent,使用Android.content.Intent.ACTION_SEND的参数来实现通过手机寄发Email的服务.实际上，收发Email的过程是通过Android内置的Gmail程序，而非使用SMTP协议主要过程是通过创建一个自定义的Intent(Android.content.Intent.ACTION_SEND)作为传送Email的Activity
```  
/* 通过Intent发送邮件 */
Intent mEmailIntent = new Intent(android.content.Intent.ACTION_SEND);
/* 设置邮件格式为plain/text */
mEmailIntent.setType("plain/text");
/* 将取得的字符串放入mEmailIntent中 */
mEmailIntent.putExtra(android.content.Intent.EXTRA_EMAIL, strEmailReciver);
mEmailIntent.putExtra(android.content.Intent.EXTRA_CC, strEmailCc);
mEmailIntent.putExtra(android.content.Intent.EXTRA_SUBJECT, strEmailSubject);
mEmailIntent.putExtra(android.content.Intent.EXTRA_TEXT, strEmailBody);
/* 打开Gmail 并将相关参数传入 */
startActivity(Intent.createChooser(mEmailIntent, getResources().getString(R.string.str_message))); 
```
工程
Android 中发送Email有很多种写法
方法一:
```  
Uri uri = Uri.parse("mailto : xxx@gmail.com");
Intent intent = new Intent(Intent.ACTION_SENDTO,uri);
```
startActivity(intent);
```  
Intent intent = new Intent(Intent.ACTION_SEND);
String[] tos = {"me@abc.com"};
String[] ccs = {"you@abc.com"};
intent.putExtra(Intent.EXTRA_EMAIL,tos);
intent.putExtra(Intent.EXTRA_CC,ccs);
intent.putExtra(Intent.EXTRA_TEXT,"The email body text"); 
intent.putExtra(Intent.EXTRA_SUBJECT,"The email subject text");
intent.setType("message/rfc822");
startActivity(Intent.createChooser(intent,"Your Client"));
```