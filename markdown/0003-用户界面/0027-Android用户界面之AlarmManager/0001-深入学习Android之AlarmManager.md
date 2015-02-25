对应AlarmManage有一个AlarmManagerServie服务程序，该服务程序才是正真提供闹铃服务的，它主要维护应用程序注册下来的各类闹铃并适时的设置即将触发的闹铃给闹铃设备（在系统中，linux实现的设备名为”/dev/alarm”），并且一直监听闹铃设备，一旦有闹铃触发或者是闹铃事件发生，AlarmManagerServie服务程序就会遍历闹铃列表找到相应的注册闹铃并发出广播。该服务程序在系统启动时被系统服务程序system_service启动并初始化闹铃设备（/dev/alarm）。当然，在JAVA层的AlarmManagerService与Linux Alarm驱动程序接口之间还有一层封装，那就是JNI。
AlarmManager将应用与服务分割开来后，使得应用程序开发者不用关心具体的服务，而是直接通过AlarmManager来使用这种服务。这也许就是客户/服务模式的好处吧。AlarmManager与 AlarmManagerServie之间是通过Binder来通信的，他们之间是多对一的关系。
在android系统中，AlarmManage提供了3个接口5种类型的闹铃服务。
3个接口：
```  
// 取消已经注册的与参数匹配的闹铃
void cancel(PendingIntent operation);
// 注册一个新的闹铃
void set(int type, long triggerAtTime, PendingIntent operation);
// 注册一个重复类型的闹铃
void setRepeating(int type, long triggerAtTime, long interval,
		PendingIntent operation);
// 设置时区
void setTimeZone(String timeZone);
// 取消已经注册的与参数匹配的闹铃
void cancel(PendingIntent operation);
// 注册一个新的闹铃
void set(int type, long triggerAtTime, PendingIntent operation);
// 注册一个重复类型的闹铃
void setRepeating(int type, long triggerAtTime, long interval,
		PendingIntent operation);
// 设置时区
void setTimeZone(String timeZone);
public static final int ELAPSED_REALTIME;
// 当系统进入睡眠状态时,这种类型的闹铃不会唤醒系统。直到系统下次被唤醒才传递它,该闹铃所用的时间是相对时间,是从系统启动后开始计时的,包括睡眠时间,可以通过调用SystemClock.elapsedRealtime()获得。系统值是3
// (0x00000003)。
public static final int ELAPSED_REALTIME_WAKEUP;
// 能唤醒系统,用法同ELAPSED_REALTIME,系统值是2 (0x00000002) 。
public static final int RTC;
// 当系统进入睡眠状态时,这种类型的闹铃不会唤醒系统。直到系统下次被唤醒才传递它,该闹铃所用的时间是绝对时间,所用时间是UTC时间,可以通过调用
// System.currentTimeMillis()获得。系统值是1 (0x00000001) 。
public static final int RTC_WAKEUP;
// 能唤醒系统,用法同RTC类型,系统值为 0 (0x00000000) 。
public static final int POWER_OFF_WAKEUP;
// 能唤醒系统,它是一种关机闹铃,就是说设备在关机状态下也可以唤醒系统,所以我们把它称之为关机闹铃。使用方法同RTC类型,系统值为4(0x00000004)。}
```
注意一个重要的参数PendingIntent。这个PendingIntent可以说是 Intent的进一步封装，他既包含了Intent的描述又是Intent行为的执行（这种定义也许不太严格），如果将Intent比作成一个订单的话，PendingIntent更像是一个下订单的人，因为它既要负责将订单发出去，也要负责订单发送后的处理，比如发送成功后要准备验收订单货物，发送失败后要重发还是取消订单等操作。开发者可以通过调用getActivity（Context， int， Intent， int）
```  
getBroadcast(Context,int,Intent,int)
getService(Context,int,Intent,int)
```
三种不同方式来得到一个PendingIntent实例。
getBroadcast——通过该函数获得的PendingIntent将会扮演一个广播的功能，就像调用Context.sendBroadcast()函数一样。当系统通过它要发送一个intent时要采用广播的形式，并且在该intent中会包含相应的intent接收对象，当然这个对象我们可以在创建PendingIntent的时候指定，也可以通过ACTION 和CATEGORY等描述让系统自动找到该行为处理对象。
```  
Intent intent = new Intent(AlarmController.this, OneShotAlarm.class);
PendingIntent sender = PendingIntent.getBroadcast(AlarmController.this, 0, intent, 0);
Intent intent = new Intent(AlarmController.this, OneShotAlarm.class);
PendingIntent sender = PendingIntent.getBroadcast(AlarmController.this, 0, intent, 0);
```
getActivity——通过该函数获得的PendingIntent可以直接启动新的activity，就像调用Context.startActivity（Intent）一样。不过值得注意的是要想这个新的Activity不再是当前进程存在的Activity 时。我们在intent中必须使用Intent.FLAG_ACTIVITY_NEW_TASK。
```  
// The PendingIntent to launch our activity if the user selects this
// notification
PendingIntent contentIntent = PendingIntent.getActivity(this, 0,
		new Intent(this, AlarmService.class), 0);
// The PendingIntent to launch our activity if the user selects this
// notification
PendingIntent contentIntent = PendingIntent.getActivity(this, 0,
		new Intent(this, AlarmService.class), 0);
```
getService——通过该函数获得的PengdingIntent可以直接启动新的Service，就像调用Context.startService()一样。
```  
// Create an IntentSender that will launch our service, to be scheduled
// with the alarm manager.
mAlarmSender = PendingIntent.getService(AlarmService.this, 0,
		new Intent(AlarmService.this, AlarmService_Service.class), 0);
```