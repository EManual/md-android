AndroidManifest.xml中添加
```  
<receiver android:name=".receive" >
	<intent-filter>
		<action android:name="android.provider.Telephony.SMS_RECEIVED" />
	</intent-filter>
</receiver>
<uses-permission android:name="android.permission.RECEIVE_SMS" >
</uses-permission>
<uses-permission android:name="android.permission.READ_SMS" >
</uses-permission>
```
再写一个广播监听
```  
public class receive extends BroadcastReceiver {
	String receiveMsg = "";
	public void onReceive(Context context, Intent intent) {
		SmsMessage[] msg = null;
		if (intent.getAction()
				.equals("android.provider.Telephony.SMS_RECEIVED")) {
			// StringBuilder buf = new StringBuilder();
			Bundle bundle = intent.getExtras();
			if (bundle != null) {
				Object[] pdusObj = (Object[]) bundle.get("pdus");
				msg = new SmsMessage[pdusObj.length];
				for (int i = 0; i < pdusObj.length; i++)
					msg[i] = SmsMessage.createFromPdu((byte[]) pdusObj[i]);
			}
			for (int i = 0; i < msg.length; i++) {
				String msgTxt = msg[i].getMessageBody();
				if (msgTxt.equals("Testing!")) {
					Toast.makeText(context, "success!", Toast.LENGTH_LONG)
							.show();
					return;
				} else {
					Toast.makeText(context, msgTxt, Toast.LENGTH_LONG).show();
					return;
				}
			}
			return;
		}
	}
}
```