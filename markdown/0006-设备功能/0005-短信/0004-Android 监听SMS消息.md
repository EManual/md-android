当设备接收到一条新的SMS消息时，就会广播一个包含了android.provider.Telephony.SMS_RECEIVED动作的Intent。注意，这个动作是一个字符串值，SDK1.0不再包含对这个字符串的引用，因此，在你的应用程序中，你需要显式的指定它。
对于应用程序监听SMS Intent广播，首先需要添加RECEIVE_SMS权限。通过在应用程序manifest中添加一个uses-permission，如下面的片段所示：
```  
<uses-permission android:name="android.permission.RECEIVE_SMS"/>
```
SMS广播Intent包含了新来SMS的细节。为了提取包装在SMS广播Intent的Bundle中的SmsMessage对象数组，使用pdus key来提取SMS pdus数组，其中，每个对象表示一个SMS消息。将每个pdu字节数组转化成SmsMessage对象，调用SmsMessage.createFromPdu，传入每个字节数组，如下面的片段所示：
```  
Bundle bundle = intent.getExtras();
if (bundle != null) {
	Object[] pdus = (Object[]) bundle.get("pdus");
	SmsMessage[] messages = new SmsMessage[pdus.length];
	for (int i = 0; i < pdus.length; i++)
		messages[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
}
```
每个SmsMessage对象包含SMS 消息的细节，包括源地址（手机号），时间和消息体。
下面的例子演示了一个Broadcast Receiver实现了onReceive函数来检查新来的短信是否以@echo字符串开始，如果是，发送相同的文本给那个手机：
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.telephony.SmsManager;
import android.telephony.SmsMessage;
public class IncomingSMSReceiver extends BroadcastReceiver {
	private static final String queryString = "@echo ";
	private static final String SMS_RECEIVED = "android.provider.Telephony.SMS_RECEIVED";
	public void onReceive(Context _context, Intent _intent) {
		if (_intent.getAction().equals(SMS_RECEIVED)) {
			SmsManager sms = SmsManager.getDefault();
			Bundle bundle = _intent.getExtras();
			if (bundle != null) {
				Object[] pdus = (Object[]) bundle.get("pdus");
				SmsMessage[] messages = new SmsMessage[pdus.length];
				for (int i = 0; i < pdus.length; i++)
					messages[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
				for (SmsMessage message : messages) {
					String msg = message.getMessageBody();
					String to = message.getOriginatingAddress();
					if (msg.toLowerCase().startsWith(queryString)) {
						String out = msg.substring(queryString.length());
						sms.sendTextMessage(to, null, out, null, null);
					}
				}
			}
		}
	}
}
```
为了监听短信，使用Intent Filter来注册Broadcast Receiver，使其监听android.provider.Telephony.SMS_RECEIVED动作，如下面的片段所示：
```  
final String SMS_RECEIVED = "android.provider.Telephony.SMS_RECEIVED";
IntentFilter filter = new IntentFilter(SMS_RECEIVED);
BroadcastReceiver receiver = new IncomingSMSReceiver();
registerReceiver(receiver, filter);
```