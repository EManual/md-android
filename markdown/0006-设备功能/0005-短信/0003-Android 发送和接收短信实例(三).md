经过上面的几个步骤发短信的功能基本就完成了，但是现在运行程序是无法工作的，主要是配置文件AndroidManifest.xml中的权限没有配置，要发送短信就要配置，发送短信的权限这样Android才会发送信息，否则发不出去信息，同样接收信息也需要有相应的接收短信的权限，在后面还要做接收短信的内容，所以在这里顺便将接收和发送短信的权限都配置好，配置代买如下：
```  
<uses-permission android:name="android.permission.SEND_SMS"/ >
<uses-permission android:name="android.permission.RECEIVE_SMS"/ >
```
可以看出来第一行是发送短信的权限，第二行是接收短信的权限
运行程序，填写正确的手机号和短信内容点击发送就可以将短信内容发送到相应的手机号上。
5、接收短信，接收短信稍有点复杂，首先创建一个接收短信的Java类文件“ReceiverDemo.java”并继承”BroadcastReceiver”
6.重写下面的方法：
```  
public void onReceive(Context context, Intent intent)
```
重写内容如下：
```  
private static final String strRes = "android.provider.Telephony.SMS_RECEIVED";
@Override
public void onReceive(Context context, Intent intent) {
	/*
	 [Tags]* 判断是否是SMS_RECEIVED事件被触发
	 [Tags]*/
	if (intent.getAction().equals(strRes)) {
		StringBuilder sb = new StringBuilder();
		Bundle bundle = intent.getExtras();
		if (bundle != null) {
			Object[] pdus = (Object[]) bundle.get("pdus");
			SmsMessage[] msg = new SmsMessage[pdus.length];
			for (int i = 0; i < pdus.length; i++) {
				msg[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
			}
			for (SmsMessage currMsg : msg) {
				sb.append("您收到了来自:【");
				sb.append(currMsg.getDisplayOriginatingAddress());
				sb.append("】的信息，内容：");
				sb.append(currMsg.getDisplayMessageBody());
			}
			Toast toast = Toast.makeText(context,
					"收到了短消息: " + sb.toString(), Toast.LENGTH_LONG);
			toast.show();
		}
	}
}
```