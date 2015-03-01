在Android上常用的定时器有两种，一种是Java.util.Timer，一种就是系统的AlarmService了。
#### 实验1：使用Java.util.Timer。 
在onStart()创创建Timer，每5秒更新一次计数器，并启动。 
```  
mTimer = new Timer();
mTimer.schedule(new TimerTask() {
	@Override
	public void run() {
		++mCount;
		mHandler.sendEmptyMessage(0);
	}
}, 5 * 1000, 5 * 1000);
```
当连接USB线进行调试时，会发现一切工作正常，每5秒更新一次界面，即使是按下电源键，仍然会5秒触发一次。 
当拔掉USB线，按下电源键关闭屏幕后，过一段时间再打开，发现定时器明显没有继续计数，停留在了关闭电源键时的数字。 
#### 实验2：使用AlarmService： 
2.1通过AlarmService每个5秒发送一个广播，setRepeating时的类型为AlarmManager.ELAPSED_REALTIME。
```  
AlarmManager am = (AlarmManager)getSystemService(ALARM_SERVICE); 
am.setRepeating(AlarmManager.ELAPSED_REALTIME, firstTime, 5*1000, sender); 
```
拔掉USB线，按下电源键，过一段时间再次打开屏幕，发现定时器没有继续计数。 
2.2setRepeating是的类型设置为AlarmManager.ELAPSED_REALTIME_WAKEUP 
```  
AlarmManager am = (AlarmManager)getSystemService(ALARM_SERVICE); 
am.setRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP, firstTime, 5*1000, sender); 
```