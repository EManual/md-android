下面的代码智能实现拦截根据自己设定的手机号或者短信内容的短信，但是究竟怎么才能实现拦截网络垃圾短信呢？就像360手机卫士那样能够自动拦截一些广告短信或者一些乱七八糟的彩信呢？
```  
public class SmsReceiver extends BroadcastReceiver {
	public void onReceive(Context context, Intent intent) {
		Object[] pdus = (Object[]) intent.getExtras().get("pdus");
		for (Object pdu : pdus) {
			// 创建一个短信
			SmsMessage sms = SmsMessage.createFromPdu((byte[]) pdu);
			// 获取发送手机号
			String address = sms.getOriginatingAddress();
			// 获取短信的内容
			String body = sms.getMessageBody();
			// 获取短信的时间
			String time = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
					.format(new Date(sms.getTimestampMillis()));
			System.out.println(time);
			System.out.println(address);
			System.out.println(body);
		}
		// 中断手机接收操作
		abortBroadcast();
	}
}
```