AlarmManager的使用机制有的称呼为全局定时器，有的称呼为闹钟。通过对它的使用，个人觉得叫全局定时器比较合适，其实它的作用和Timer有点相似。都有两种相似的用法：（1）在指定时长后执行某项操作（2）周期性的执行某项操作。
AlarmManager对象配合Intent使用，可以定时的开启一个Activity，发送一个BroadCast，或者开启一个Service。
下面的代码详细的介绍了两种定时方式的使用：
（1）在指定时长后执行某项操作
代码　
```  
//操作：发送一个广播，广播接收后Toast提示定时操作完成     
Intent intent =new Intent(Main.this, alarmreceiver.class);
intent.setAction("short");
PendingIntent sender = PendingIntent.getBroadcast(Main.this, 0, intent, 0);
//设定一个五秒后的时间
Calendar calendar=Calendar.getInstance();
calendar.setTimeInMillis(System.currentTimeMillis());
calendar.add(Calendar.SECOND, 5);
AlarmManager alarm=(AlarmManager)getSystemService(ALARM_SERVICE);
alarm.set(AlarmManager.RTC_WAKEUP, calendar.getTimeInMillis(), sender);
//或者以下面方式简化
//alarm.set(AlarmManager.RTC_WAKEUP, System.currentTimeMillis()+5*1000, sender);
Toast.makeText(Main.this, "五秒后alarm开启", Toast.LENGTH_LONG).show();
//注意：receiver记得在manifest.xml注册
```
代码 
```  
public static class alarmreceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		if (intent.getAction().equals("short")) {
			Toast.makeText(context, "short alarm", Toast.LENGTH_LONG).show();
		} else {
			Toast.makeText(context, "repeating alarm", Toast.LENGTH_LONG).show();
		}
	}
}
```
（2）周期性的执行某项操作
```  
Intent intent = new Intent(Main.this, alarmreceiver.class);
intent.setAction("repeating");
PendingIntent sender = PendingIntent.getBroadcast(Main.this, 0, intent, 0);
//开始时间
long firstime = SystemClock.elapsedRealtime();
AlarmManager am = (AlarmManager)getSystemService(ALARM_SERVICE);
//5秒一个周期，不停的发送广播
am.setRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP, firstime, 5*1000, sender);
```
AlarmManager的setRepeating()相当于Timer的Schedule(task,delay,peroid);有点差异的地方时Timer这个方法是指定延迟多长时间
以后开始周期性的执行task;
AlarmManager的取消：（其中需要注意的是取消的Intent必须与启动Intent保持绝对一致才能支持取消AlarmManager）
```  
Intent intent =new Intent(Main.this, alarmreceiver.class);
intent.setAction("repeating");
PendingIntent sender=PendingIntentgetBroadcast(Main.this, 0, intent, 0);
AlarmManager alarm=(AlarmManager)getSystemService(ALARM_SERVICE);
alarm.cancel(sender);
```