当android系统接收到短信时，会发送一个广播BroadcastReceiver，这个广播是以有序广播的形式发送的。
所谓的有序广播就是广播发出后，接收者是按照设置的优先级一个一个接着接收，前面的接收者可以选择是否终止这条广播以使后面的接收者接收不到，而普遍广播发送后所有的接收者都能同时接到，但是不能终止这条广播，也不能将它的处理结果传递给下个接收者。
今天实现的sms拦截就是通过实现一个BroadcastReceiver并将其的优先级设置的比系统sms接收者高。接下来给大家进行仔细讲解。
首先实现一个BroadcastReceiver：
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.telephony.SmsMessage;
import android.util.Log;
public class SMSReceive extends BroadcastReceiver {
	static final String TAG = "SMSReceive";
	static final String smsuri = "android.provider.Telephony.SMS_RECEIVED";
	@Override
	public void onReceive(Context arg0, Intent arg1) {
		if (arg1.getAction().equals(smsuri)) {
			Bundle bundle = arg1.getExtras();
			if (null != bundle) {
				Object[] pdus = (Object[]) bundle.get("pdus");
				SmsMessage[] smg = new SmsMessage[pdus.length];
				for (int i = 0; i < pdus.length; i++) {
					smg[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
					Log.i(TAG + "smg" + i, smg[i].toString());
				}
				for (SmsMessage cursmg : smg) {
					String codeStr = cursmg.getDisplayMessageBody();
					String codeStr2 = cursmg.getDisplayOriginatingAddress();
					String codeStr3 = cursmg.getMessageBody();
					String codeStr6 = cursmg.getOriginatingAddress();
					Log.i(TAG + "codeStr", codeStr);
					Log.i(TAG + "codeStr2", codeStr2);
					Log.i(TAG + "codeStr3", codeStr3);
					Log.i(TAG + "codeStr6", codeStr6);
				}
				abortBroadcast(); // 终止此条广播
			}
		}
	}
}
```
然后，我们还要注册它，android:priority就是设置优先级的。
Xml代码：
```  
<receiver android:name="SMSReceive">  
     <intent-filter android:priority="100">
         <action android:name="android.provider.Telephony.SMS_RECEIVED" />
     </intent-filter>
</receiver> 
```
此为，不要忘了添加接收sms的权限：
Xml代码：
```  
<uses-permission android:name="android.permission.RECEIVE_SMS"></uses-permission>
```
好了，短信拦截就实现了，如果想实现短信黑名单，只需要代码中获取到的号码和已设置的号码匹配然后做相关操作就可以了。