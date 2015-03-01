Andriod通话处理流程
#### 一、总览 
```  
1、从java端发送at命令的处理流程。 
2、unsolicited 消息从modem上报到java的流程。 
3、猫相关的各种状态的监听和通知机制。 
4、通话相关的图标变换的工作原理。 
5、gprs拨号上网的通路原理。 
6、通话相关的语音通路切换原理、震动接口。 
7、通话相关的notification服务。 
8、通话相关的各种server。 
```
第一部分：从java端发送at命令的处理流程。 
拨出电话流程： 
1、contacts的androidmanifest.xml android:process="android.process.acore"说明此应用程序运行在acore进程中。 
DialtactsActivity的intent-filter的action属性设置为main，catelog属性设置为launcher，所以此activity能出现在主菜单中，并且是点击此应用程序的第一个界面。dialtactsactivity包含四个tab，分别由TwelveKeyDialer,RecentCallsListActivity，两个activity-alias DialtactsContactsEntryActivity和DialtactsFavoritesEntryActivity分别表示联系人和收藏tab，但是正真的联系人列表和收藏是由ContactsListActivity负责。
2、进入TwelveKeyDialer OnClick方法，按住的按钮id为：R.id.digits，执行 
```  
placecall()
Intent intent = new Intent(Intent.ACTION_CALL_PRIVILEGED,
Uri.fromParts("tel", number, null));
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent);
```
3、intert.ACTION_CALL_PRIVILEGED实际字符串为android.intent.action.CALL_PRIVILEGED，通过查找知道了packegs/phone
下面的androidmanifest.xml中PrivilegedOutgoingCallBroadcaster activity-alias设置了intent-filter，所以需要找到其targetactivity为OutgoingCallBroadcaster。所以进入OutgoingCallBroadcaster的 onCreate() //如果为紧急号码马上启动intent.setClass(this, InCallScreen.class); startActivity(intent); 
```  
Intent broadcastIntent = new Intent(Intent.ACTION_NEW_OUTGOING_CALL);
if (number != null) 
	broadcastIntent.putExtra(Intent.EXTRA_PHONE_NUMBER, number);
broadcastIntent.putExtra(EXTRA_ALREADY_CALLED, callNow);
broadcastIntent.putExtra(EXTRA_ORIGINAL_URI, intent.getData().toString());
if (LOGV) 
	Log.v(TAG, "Broadcasting intent " + broadcastIntent + ".");
sendOrderedBroadcast(broadcastIntent, PERMISSION, null, null, Activity.RESULT_OK, number, null);
```
4、Intent.ACTION_NEW_OUTGOING_CALL实际字符串为android.intent.action.NEW_OUTGOING_CALL，通过查找知道了packegs/phone 
下面的androidmanifest.xml中OutgoingCallReceiver Receiver接收此intent消息。找到OutgoingCallReceiver，执行 onReceive()函数
```  
Intent newIntent = new Intent(Intent.ACTION_CALL, uri);
newIntent.putExtra(Intent.EXTRA_PHONE_NUMBER, number);
newIntent.setClass(context, InCallScreen.class);
newIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
```
5、请求拨号的java部分流程 
onCreate(第一次)/onNewIntent(非第一次) 
```  
internalResolveIntent
placeCall(intent);
PhoneUtils.placeCall(mPhone, number, intent.getData());
phone.dial(number);
mCT.dial(newDialString);
dial(dialString, CommandsInterface.CLIR_DEFAULT);
cm.dial(pendingMO.address, clirMode, obtainCompleteMessage());//obtainCompleteMessage(EVENT_OPERATION_COMPLETE);
send(rr);
msg = mSender.obtainMessage(EVENT_SEND, rr);
acquireWakeLock();
msg.sendToTarget();
RILSender.handleMessage()
case EVENT_SEND: 
s.getOutputStream().write(dataLength);
s.getOutputStream().write(data);//从这里流程跑到下面ril.cpp中监听部份
```
6、请求拨号的c/c++部分流程 
6.1、初始化事件循环，启动串口监听，注册socket监听。 
rild.c->main() 
(1)、RIL_startEventLoop 
```  
//建立事件循环线程 
ret = pthread_create(&s_tid_dispatch, &attr, eventLoop, NULL);
//注册进程唤醒事件回调 
ril_event_set (&s_wakeupfd_event, s_fdWakeupRead, true,
processWakeupCallback, NULL);
rilEventAddWakeup (&s_wakeupfd_event);
//建立事件循环 
ril_event_loop
for (;;) { 
	...
	n = select(nfds, &rfds, NULL, NULL, ptv);
	// Check for timeouts 
	processTimeouts();
	// Check for read-ready 
	processReadReadies(&rfds, n);
	// Fire away 
	firePending();
} 
```