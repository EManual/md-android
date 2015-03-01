为了追踪发出的SMS消息的传送和成功送达，实现并注册Broadcast Receiver来监听你在sendTextMessage方法中传入的PendingIntent的动作。
第一个PendingIntent参数，sentIntent，当消息发送成功或发送失败时触发。Broadcast Receiver接收到这个Intent时得到的结果值将是下面中的一个：
1)Activity.RESULT_OK
表示成功发送。
2)SmsManager.RESULT_ERROR_GENERIC_FAILURE
表示一个未指定的失败。
3)SmsManager.RESULT_ERROR_RADIO_OFF
表示无线连接关闭。
4)SmsManager.RESULT_ERROR_NULL_PDU
表示一个PDU失败。
第二个PendingIntent参数，deliveryIntent，仅在当目标用户接收到你的SMS消息后触发。
接下来的代码片段显示了发送一个SMS短信和监视短信的传送和成功送达的典型代码：
```  
String SENT_SMS_ACTION = "SENT_SMS_ACTION";
String DELIVERED_SMS_ACTION = "DELIVERED_SMS_ACTION";
// 创造sentIntent参数
Intent sentIntent = new Intent(SENT_SMS_ACTION);
PendingIntent sentPI = PendingIntent.getBroadcast(getApplicationContext(),0,sentIntent,0);
// 创造deliveryIntent参数
Intent deliveryIntent = new Intent(DELIVERED_SMS_ACTION);
PendingIntent deliverPI = PendingIntent.getBroadcast(getApplicationContext(),0,deliveryIntent,0);
// 登记广播接收器
registerReceiver(new BroadcastReceiver() {
	@Override
	public void onReceive(Context _context, Intent _intent) {
	switch (getResultCode()) {
	case Activity.RESULT_OK:
	[… send success actions … ];
	break;
	case SmsManager.RESULT_ERROR_GENERIC_FAILURE:
	[… generic failure actions … ];
	break;
	case SmsManager.RESULT_ERROR_RADIO_OFF:
	[… radio off failure actions … ];
	break;
	case SmsManager.RESULT_ERROR_NULL_PDU:
	[… null PDU failure actions … ];
	break;
	}
	}
},new IntentFilter(SENT_SMS_ACTION));

registerReceiver(new BroadcastReceiver() {
	@Override
	public void onReceive(Context _context, Intent _intent) {
		[… SMS delivered actions … ]
	}
},new IntentFilter(DELIVERED_SMS_ACTION));
// 发送信息时
smsManager.sendTextMessage(sendTo, null, myMessage, sentPI, deliverPI);
```
监视发出的SMS消息
Android调试支持在多个模拟器实例之间发送SMS消息。从一个模拟器发送一个SMS到另一个，指定目标模拟器的端口号作为发送新消息的"to"地址。
Android会自动地将你的消息发送到目标模拟器实例上，在那里，消息会像一般SMS一样被处理。
确认短信的大小
SMS消息一般限制在160个字符，所以，更大的消息需要分解成一系列细小部分。SMS Manager包含divideMessage方法，它接收一个字符串并将其分解成一个消息数组，其中的每个字符串都小于允许的长度。使用sendMultipartTextMessage来发送消息数组，如下面的片段所示：
```  
ArrayList<String> messageArray = smsManager.divideMessage(myMessage);
ArrayList<PendingIntent> sentIntents = new ArrayList<PendingIntent>();
for (int i = 0; i < messageArray.size(); i++)
	sentIntents.add(sentPI);
smsManager.sendMultipartTextMessage(sendTo,null,messageArray,sentIntents, null); 
```
sendMultipartTextMessage方法中的sentIntent和deliveryIntent参数是ArrayList类型，它可以为每个消息部分指定不同的PendingIntent。