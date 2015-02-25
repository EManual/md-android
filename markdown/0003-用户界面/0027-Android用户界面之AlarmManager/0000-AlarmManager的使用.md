AlarmManager--全局定时器
（1）在指定时长后执行某项操作
```  
// 发送一个广播，广播接收后Toast提示定时操作完成
Intent intent = newIntent(Main.this, alarmreceiver.class);
intent.setAction("short");
PendingIntent sender = PendingIntent.getBroadcast(Main.this, 0, intent, 0);
// 设定一个五秒后的时间
Calendar calendar = Calendar.getInstance();
calendar.setTimeInMillis(System.currentTimeMillis());
calendar.add(Calendar.SECOND, 5);
AlarmManager alarm = (AlarmManager) getSystemService(ALARM_SERVICE);
alarm.set(AlarmManager.RTC_WAKEUP, calendar.getTimeInMillis(), sender);
Toast.makeText(Main.this, "五秒后alarm开启", Toast.LENGTH_LONG).show();
```
（2）周期性的执行某项操作
```  
Intent intent = newIntent(Main.this, alarmreceiver.class);
intent.setAction("repeating");
PendingIntent sender = PendingIntent.getBroadcast(Main.this, 0, intent, 0);
// 开始时间
long firstime = SystemClock.elapsedRealtime();
AlarmManager am = (AlarmManager) getSystemService(ALARM_SERVICE);
// 5秒一个周期，不停的发送广播 am.setRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP,
// firstime, 5*1000, sender);
// 注意：receiver记得在manifest.xml注册
public static class alarmreceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		// TODO Auto-generated method stub
		if (intent.getAction().equals("short")) {
			Toast.makeText(context, "short alarm", Toast.LENGTH_LONG)
					.show();
		} else {
			Toast.makeText(context, "repeating alarm",
					Toast.LENGTH_LONG).show();
		}
	}
}
```
AlarmManager的取消：（其中需要注意的是取消的Intent必须与启动Intent保持绝对一致才能支持取消AlarmManager）
```  
Intent intent = new Intent(Main.this, alarmreceiver.class);
intent.setAction("repeating");
PendingIntent sender = PendingIntent.getBroadcast(Main.this, 0, intent, 0);
AlarmManager alarm = (AlarmManager) getSystemService(ALARM_SERVICE);
alarm.cancel(sender);
```