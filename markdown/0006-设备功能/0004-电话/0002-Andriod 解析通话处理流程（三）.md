c++部份 
```  
readerLoop()
line = readline();
processLine(line);
handleUnsolicited(line);
if (s_unsolHandler != NULL) { 
s_unsolHandler (line1, line2);//实际执行的是void onUnsolicited (const char *s, const char *sms_pdu)
if (strStartsWith(s,"+CRING:") 
	|| strStartsWith(s,"RING")
	|| strStartsWith(s,"NO CARRIER")
	|| strStartsWith(s,"+CCWA")
)
RIL_onUnsolicitedResponse (RIL_UNSOL_RESPONSE_CALL_STATE_CHANGED, NULL, 0);
p.writeInt32 (RESPONSE_UNSOLICITED);
p.writeInt32 (unsolResponse);
ret = s_unsolResponses[unsolResponseIndex].responseFunction(p, data, datalen);
ret = sendResponse(p);
sendResponseRaw(p.data(), p.dataSize());
ret = blockingWrite(fd, (void *)&header, sizeof(header));
blockingWrite(fd, data, dataSize);
```
java部份
```  
ril.java->RILReceiver.run()
for(;;){
	length = readRilMessage(is, buffer);
	p = Parcel.obtain();
	p.unmarshall(buffer, 0, length);
	p.setDataPosition(0);
	processResponse(p);
	processUnsolicited (p);
	response = p.readInt();
	switch(response) {
	...
	case RIL_UNSOL_RESPONSE_CALL_STATE_CHANGED: 
		ret = responseVoid(p); break;
	...
	} 
	switch(response) {
	case RIL_UNSOL_RESPONSE_CALL_STATE_CHANGED: 
		if (RILJ_LOGD) unsljLog(response); 
			mCallStateRegistrants.notifyRegistrants(new AsyncResult(null, null, null));
			...
	} 
}
```
第三部分、第四部分：猫相关的各种状态的监听和通知机制／通话相关的图标变换的工作原理。 网络状态，edge,gprs图标的处理
a、注册监听部分
```  
==>SystemServer.java
init2()
Thread thr = new ServerThread();
thr.setName("android.server.ServerThread");
thr.start();
ServerThread.run() com.android.server.status.StatusBarPolicy.installIcons(context, statusBar);
sInstance = new StatusBarPolicy(context, service);
// phone_signal 
mPhone = (TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE);
mPhoneData = IconData.makeIcon("phone_signal",
null, com.android.internal.R.drawable.stat_sys_signal_null, 0, 0); 
mPhoneIcon = service.addIcon(mPhoneData, null);
// register for phone state notifications. 
((TelephonyManager)mContext.getSystemService(Context.TELEPHONY_SERVICE)).listen(mPhoneStateListener,
	PhoneStateListener.LISTEN_SERVICE_STATE
	| PhoneStateListener.LISTEN_SIGNAL_STRENGTH
	| PhoneStateListener.LISTEN_CALL_STATE
	| PhoneStateListener.LISTEN_DATA_CONNECTION_STATE
	| PhoneStateListener.LISTEN_DATA_ACTIVITY);
//实际是调用的是TelephonyRegistry.listen，此listen函数会将Iphonestatelistener添加到对应的的handler数组中，到时来了事件会轮询回调。 
// data_connection 
mDataData = IconData.makeIcon("data_connection", null, com.android.internal.R.drawable.stat_sys_data_connected_g, 0, 0);
mDataIcon = service.addIcon(mDataData, null);
service.setIconVisibility(mDataIcon, false);

b、事件通知部分
==>PhoneFactory.java
makeDefaultPhones()
sPhoneNotifier = new DefaultPhoneNotifier();
useNewRIL(context);
phone = new GSMPhone(context, new RIL(context), sPhoneNotifier);
for example 
==>DataConnectionTracker.java
notifyDefaultData(String reason)
phone.notifyDataConnection(reason);
mNotifier.notifyDataConnection(this, reason);
==>DefaultPhoneNotifier.java
mRegistry = ITelephonyRegistry.Stub.asInterface(ServiceManager.getService(
"telephony.registry")); 
mRegistry.notifyDataConnection(convertDataState(sender.getDataConnectionState()),
sender.isDataConnectivityPossible(), reason, sender.getActiveApn(),
sender.getInterfaceName(null));
```