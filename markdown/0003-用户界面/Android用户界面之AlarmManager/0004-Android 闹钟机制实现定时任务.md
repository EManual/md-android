Android的闹钟实现机制很简单，只需要调用AlarmManager.set（）将闹铃时间记录到系统中，当闹铃时间到后，系统会给应用程序发送广播，我们只需要去注册广播接收器就可以了。
本文分三部分讲解如何实现闹钟：
目录：
1. 设置闹铃时间;
2. 接收闹铃事件广播;
3. 重开机后重新计算并设置闹铃时间;
正文：
1. 设置闹铃时间(毫秒)
```  
private void setAlarmTime(Context context,  long timeInMillis) {
	AlarmManager am = (AlarmManager)context.getSystemService(Context.ALARM_SERVICE);
	Intent intent = new Intent("android.alarm.demo.action");
	PendingIntent sender = PendingIntent.getBroadcast(
			context, 0, intent, PendingIntent.FLAG_CANCEL_CURRENT);
	int interval = 60 * 1000;//闹铃间隔， 这里设为1分钟闹一次，在第2步我们将每隔1分钟收到一次广播
	am.setRepeating(AlarmManager.RTC_WAKEUP, timeInMillis, interval, sender)
}
```
2. 接收闹铃事件广播
```  
public class AlarmReceiver extends BroadcastReceiver {
    public void onReceive(Context context, Intent intent) {
        if ("android.alarm.demo.action".equals(intent.getAction())) {
            //第1步中设置的闹铃时间到，这里可以弹出闹铃提示并播放响铃
            //可以继续设置下一次闹铃时间;
            return;
        }
    }
}
```
当然，Receiver是需要在Manifest.xml中注册的：
```  
<receiver android:name="AlarmReceiver">
	<intent-filter>
		<action android:name="android.alarm.demo.action" />
	</intent-filter>
</receiver>
```
3. 重开机后重新计算并设置闹铃时间
当然要有一个BootReceiver：
```  
public class BootReceiver extends BroadcastReceiver {
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (action.equals(Intent.ACTION_BOOT_COMPLETED)) {
            //重新计算闹铃时间，并调第一步的方法设置闹铃时间及闹铃间隔时间
        }
    }
}
```
当然，也需要注册：
```  
<receiver android:name="BootReceiver">
	<intent-filter>
		<action android:name="android.intent.action.BOOT_COMPLETED" />
	</intent-filter>
</receiver>
```
闹钟实现原理其实就这么多，至于具体的细节比如闹铃时间存储及计算， 界面显示及闹铃提示方式，每个人的想法做法都会不一样，就不赘述。