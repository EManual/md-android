前段时间在一个应用中调用系统自带的发送短信的Intent，但是接收者的号码一直穿不过去，代码如下：
```  
Uri smsToUri = Uri.parse("smsto:123456");
Intent sendIntent = new Intent(Intent.ACTION_VIEW, smsToUri);
sendIntent.putExtra("sms_body", "Hello dear world");
sendIntent.setType("vnd.android-dir/mms-sms");
startActivity(sendIntent);
```
然后查到原因是这个Uri格式的无法自动解析出来，需要另外设置下接收者地址，代码如下：
```  
sendIntent.putExtra("address", "123456");
```