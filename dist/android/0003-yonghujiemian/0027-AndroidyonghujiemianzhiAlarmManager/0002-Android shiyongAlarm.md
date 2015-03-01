Alarm是在预定的时间上触发Intent的一种独立的方法。
Alarm超出了应用程序的作用域，所以它们可以用于触发应用程序事件或动作，甚至在应用程序关闭之后。与Broadcast Receiver结合，它们可以变得尤其的强大，可以通过设置Alarm来启动应用程序或者执行动作，而应用程序不需要打开或者处于活跃状态。
举个例子，你可以使用Alarm来实现一个闹钟程序，执行正常的网络查询，或者在“非高峰”时间安排耗时或有代价的操作。
对于仅在应用程序生命周期内发生的定时操作，Handler类与Timer和Thread类的结合是一个更好的选择，它允许Android更好地控制系统资源。
Android中的Alarm在设备处于睡眠模式时仍保持活跃，它可以设置来唤醒设备；然而，所有的Alarm在设备重启时都会被取消。
Alarm的操作通过AlarmManager来处理，通过getSystemService可以获得其系统服务，如下所示：
```  
AlarmManager alarms = (AlarmManager)getSystemService(Context.ALARM_SERVICE);
```
为了创建一个新的Alarm，使用set方法并指定一个Alarm类型、触发时间和在Alarm触发时要调用的Intent。如果你设定的Alarm发生在过去，那么，它将立即触发。
这里有4种Alarm类型。你的选择将决定你在set方法中传递的时间值代表什么，是特定的时间或者是时间流逝：
```  
RTC_WAKEUP
在指定的时刻（设置Alarm的时候），唤醒设备来触发Intent。
RTC
在一个显式的时间触发Intent，但不唤醒设备。
ELAPSED_REALTIME
从设备启动后，如果流逝的时间达到总时间，那么触发Intent，但不唤醒设备。流逝的时间包括设备睡眠的任何时间。注意一点的是，时间流逝的计算点是自从它最后一次启动算起。
ELAPSED_REALTIME_WAKEUP
从设备启动后，达到流逝的总时间后，如果需要将唤醒设备并触发Intent。
```
Alarm的创建过程演示如下片段所示：
```  
int alarmType = AlarmManager.ELAPSED_REALTIME_WAKEUP;
long timeOrLengthofWait = 10000;
String ALARM_ACTION = "ALARM_ACTION";
Intent intentToFire = new Intent(ALARM_ACTION);
PendingIntent pendingIntent = PendingIntent.getBroadcast(this, 0, intentToFire, 0);
alarms.set(alarmType, timeOrLengthofWait, pendingIntent);
```
当Alarm到达时，你指定的PendingIntent将被触发。设置另外一个Alarm并使用相同的PendingIntent来替代之前存在的Alarm。
取消一个Alarm，调用AlarmManager的cancel方法，传入你不再希望被触发的PendingIntent，如下面的代码所示：
```  
alarms.cancel(pendingIntent);
```
接下来的代码片段中，设置了两个Alarm，随后马上取消了第一个Alarm。第一个Alarm显式地设置了在特定的时间唤醒设备并发送Intent。第二个设置为从设备启动后，流逝时间为30分钟，到达时间后如果设备在睡眠状态也不会唤醒它。
```  
AlarmManager alarms = (AlarmManager)getSystemService(Context.ALARM_SERVICE);
String MY_RTC_ALARM = "MY_RTC_ALARM";
String ALARM_ACTION = "MY_ELAPSED_ALARM";
PendingIntent rtcIntent = PendingIntent.getBroadcast(this, 0, new Intent(MY_RTC_ALARM), 1);
PendingIntent elapsedIntent = PendingIntent.getBroadcast(this, 0, new Intent(ALARM_ACTION), 1);
Date t = new Date();
t.setTime(java.lang.System.currentTimeMillis() + 60*1000*5);
alarms.set(AlarmManager.RTC_WAKEUP, t.getTime(), rtcIntent);
alarms.set(AlarmManager.ELAPSED_REALTIME, 30*60*1000, elapsedIntent);
alarms.cancel(rtcIntent);
```