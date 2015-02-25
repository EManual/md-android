今天要做的一个功能是当电话在通话的时候，自己开发的应用UI界面也要随之更改，当对方挂掉电话之后，UI界面又会回到原来的状态，说白了就是，能知道系统是否在打电话来。
那么 有两种情况：
1.被动的知道，也就是用广播接收，或是listener。
2.主动的知道，也就是通过系统api，得到当前的状态是什么。
如果能同时掌握这两种方式，就能达到实时的更新ui。
先看被动的：
第一步：注册监听(静态)
```  
<receiver android:name="com.sun.shine.PhoneReceiver" >
	<intent-filter>
		<action android:name="android.intent.action.PHONE_STATE" />
	</intent-filter>
</receiver>
```
第二步：PhoneReceiver.java
```  
@Override
public void onReceive(Context context, Intent intent) {
	String s = intent.getStringExtra("state");
	if ("OFFHOOK".equals(s) || "RINGING".equals(s)) {
		update(true);// 更新ui
	} else {
		update(false);// 更新ui
	}
}
```
第二种方式：主动，只要在Activity添加此代码就能判断是否在打电话
```  
boolean isCalling() {
	TelephonyManager telephonymanager = (TelephonyManager) this
			.getSystemService(Context.TELEPHONY_SERVICE);

	switch (telephonymanager.getCallState()) {
	case TelephonyManager.CALL_STATE_OFFHOOK:
	case TelephonyManager.CALL_STATE_RINGING:
		return true;
	default: {
		return false;
	}
	}
}
```