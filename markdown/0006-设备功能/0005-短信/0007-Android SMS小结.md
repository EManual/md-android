1.在Android中，SMS消息传递是由SmsManager进行处理的。可以通过静态方法SmsManager.getDefault()来获得对SmsManager的引用，如下：
```  
SmsManager smsManager = SmsManager.getDefault();
```
2.Android中，要接收和发送SMS需要以下两个权限
```  
<uses-permission android:name="android.permission.READ_SMS" />  
<uses-permission android:name="android.permission.RECEIVE_SMS" />
```
3.SMS的发送
1) 发送文本信息，可以使用SMS Manager 中的sendTextManager 的方法
```  
sendTextMessage(destinationAddress, scAddress, text, sentIntent, deliveryIntent)；
```
参数如下：
```  
destinationAddress:接收方的手机号码
scAddress:发送方的手机号码
text:信息内容
sentIntent:发送是否成功的回执，会在消息发送成功或者失败后触发。
DeliveryIntent:接收是否成功的回执，当目标接收人收到你的信息后触发。
```
2)跟踪和确认SMS消息的发送
```  
sendTextMessage(destinationAddress, scAddress, text, sentIntent, deliveryIntent)；
```
参数sentlntent的返回码如下：
```  
Activity.RESULT_OK：表示发送成功
RESULT_ERROR_GENERIC_FAILURE ：表示发生了为指定的错误
RESULT_ERROR_RADIO_OFF ：表示连接的无线信号被 关闭
RESULT_ERROR_NULL_PDU：表示PDU错误
```
3)发送SMS以及监控它的发送过程是否成功的经典示例：
```  
String SEND_SMS_ACTION = "SENT_SMS_ACTION";
String DELIVERED_SMS_ACTION = "DELIVERED_SMS_ACTION";
// 创建senTIntent参数
Intent sentIntent = new Intent(SEND_SMS_ACTION);
PendingIntent sentPI = PendingIntent.getBroadcast(
		getApplicationContext(), 0, sentIntent, 0);
// 创建deliveredIntent参数
Intent deliveredIntent = new Intent(DELIVERED_SMS_ACTION);
PendingIntent delivePI = PendingIntent.getBroadcast(getApplicationContext(), 0, deliveredIntent, 0);
// 注册广播器
registerReceiver(new BroadcastReceiver() {
	@Override
	public void onReceive(Context context, Intent intent) {
		switch (getResultCode()) {
		case Activity.RESULT_OK:
		case RESULT_ERROR_GENERIC_FAILURE:
		case RESULT_ERROR_RADIO_OFF:
		case RESULT_ERROR_NULL_PDU:
		}
	}
}, new IntentFilter(SEND_SMS_ACTION));
// 注册广播器
registerReceiver(new BroadcastReceiver() {
	@Override
	public void onReceive(Context context, Intent intent) {
	}
}, new IntentFilter(DELIVERED_SMS_ACTION));
```
4)保证不超过最大的SMS信息大小
SMS的大小一般被限制为160个字符，比它大的信息会被分割为多个小的部分。SMS Manager的divideMeaasge方法可以接收一个字符串作为输入，并把他分割到一个消息的ArrayList中，每一个消息都比允许的最大长度小。使用sendMultipartTextMessage可以发送消息数组。如下：
```  
ArrayList<String> messageArray = smsManager.divideMeaasge(myMessage)；
ArrayList<PendingIntent> sentIntents = new ArrayList<Pendinglntent>();
for(int i = 0; i < messageArrsy.size(); i ++)
{
	sentIntents.add(sentPI);
    smsManager.sendMultipartTextMessage(sendTo,null,messageArray,sentintent,null);
}
```
5)发送数据消息
使用SMS Manager的sendDataMessage方法，可以经由SMS来发送二进制数据。
sendDataMessage与sendTextMessage方法相似，前者需要额外的参数：信息到达的目的端口和由你想发送的数据所组成的一个字节数组
```  
short destinationPort = 80;
byte[] data = [...you data ...];
smsManager.sendDataMessage(sendTo,null,destinationPort ,data,sentPI,null);
```
4.SMS的监听
SMS广播Intent包含了收到的SMS的详细信息。要提取封装在SMS广播的SmsManager对象，需要使用PDU密钥来提取一个SMS
pdus数组，其中每一个pdu都表示一条SMS信息。
如下：
```  
Bundle bundle = intent.getExtras();
Object[] pdus = (Object[]) bundle.get("pdus");
SmsMessage[] msgs = new SmsMessage[pdus.length];
for (int i = 0; i < pdus.length; i++) {
	msgs[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
}
```
每一个SMS Manager对象都包含了SMSManager信息的详细内容（电话号码，时间戳，信息体）。
要监听到来的信息，需要使用一个监听android.provider.Telephony.SMS_RECEIVED动作串的Intent Filter 来注册一个广播接收器。如下：
```  
final String SMS_RECEIVED = "android.provider.Telephony.SMS_RECEIVED动作串的Intent Filter";
IntentFilter filter = new IntentFilter(SMS_RECEIVED);
BroadcastReceiver receiver = new IncomingSMSReceiver();
registerReceiver(receiver, filter);　
```