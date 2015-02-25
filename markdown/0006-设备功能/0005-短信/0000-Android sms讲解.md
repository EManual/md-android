#### 一、android sms所要的权限
```  
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
```
#### 二、sms发送 
与短消息发送相关的类为：SmsManager. 
```  
SmsManager.sendTextMessage(destinationAddress, scAddress, text, sentIntent, deliveryIntent);
PendingIntent sentIntent = PendingIntent.getBroadcast(this, 0, new Intent("SMS_SENT"), 0);
Intent deliveryIntent = PendingIntent.getBroadcast(this, 0, new Intent("SMS_DELIVERED"), 0);
```
并注册接收器,根据getResultCode()来判断：
```  
registerReceiver(sendReceiver); 
registerReceiver(deliveredReceiver); 
```
#### 三、sms接收 
根据接收时广播的android.provider.Telephony.SMS_RECEIVED的Intent.我们可以扩展一个BroadcastReceiver来接收sms。 
传递的Intent包含pdus数据。相关代码如下：
```  
Bundle bundle = intent.getExtras();
Object[] pdus = (Object[]) bundle.get("pdus");
SmsMessage[] msgs = new SmsMessage[pdus.length];
for (int i = 0; i < pdus.length; i++) { 
	msgs[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
} 
Bundle bundle = intent.getExtras();
Object[] pdus = (Object[]) bundle.get("pdus");
SmsMessage[] msgs = new SmsMessage[pdus.length];
for (int i = 0; i < pdus.length; i++) {
	msgs[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
}
```
#### 四、采用ContentObserver监控短信数据库 
上面方法三中并不能对sms进行更新和删除操作，要做这些操作需要用ContentObserver来监控短信数据库的变化来进行相关操作。
1.短信数据库的ContentUri
```  
public final static String SMS_URI_ALL = "content://sms/"; //0 
public final static String SMS_URI_INBOX = "content://sms/inbox";//1 
public final static String SMS_URI_SEND = "content://sms/sent";//2 
public final static String SMS_URI_DRAFT = "content://sms/draft";//3 
public final static String SMS_URI_OUTBOX = "content://sms/outbox";//4 
public final static String SMS_URI_FAILED = "content://sms/failed";//5 
public final static String SMS_URI_QUEUED = "content://sms/queued";//6 
```
2.sms主要结构：
```  
_id => 短消息序号 如100 
thread_id => 对话的序号 如100 
address => 发件人地址，手机号.如+8613811810000 
person => 发件人，返回一个数字就是联系人列表里的序号，陌生人为null 
date => 日期 long型。如1256539465022 
protocol => 协议 0 SMS_RPOTO, 1 MMS_PROTO 
read => 是否阅读 0未读， 1已读 
status => 状态 -1接收，0 complete, 64 pending, 128 failed 
type => 类型 1是接收到的，2是已发出 
body => 短消息内容 
service_center => 短信服务中心号码编号。如+8613800755500 
```
3.步骤 
a.写一个类继承ContentObserver
```  
public class SMSDBObserver extends ContentObserver 
```
重写onChange方法(里面对INBOX, SEND两个URI进行处理) 
```  
public void onChange(boolean selfChange) 
Uri smsInBox = Uri.parse(SMS_URI_INBOX);
Cursor c = ctx.getContentResolver().query(uriSms, null, selection, selectionArgs, sortOrder);
//对字段进行操作。。。 
//ctx.getContentResolver().query(uri, projection, selection, selectionArgs, sortOrder); 
//ctx.getContentResolver().update(uri, values, where, selectionArgs); 
//ctx.getContentResolver().delete(url, where, selectionArgs); 
//ctx.getContentResolver().insert(url, values); 
```
b.在Activity中注册短信监控
```  
// 监控短信 
smsObserver = new SMSDBObserver(new Handler(), this, app);
getContentResolver().registerContentObserver(Uri.parse(SMSDBObserver.SMS_URI_ALL), true, smsObserver);
```