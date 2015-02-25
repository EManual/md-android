第四部分：gprs拨号上网的通路原理。 
上层java程序调用gprs流程： 
```  
=>PhoneApp.java
onCreate()
PhoneFactory.makeDefaultPhones(this);
phone = new GSMPhone(context, new SimulatedCommands(), sPhoneNotifier);
mDataConnection = new DataConnectionTracker (this);
createAllPdpList();//建立缺省pdpconnection
pdp = new PdpConnection(phone);
dataLink = new PppLink(phone.mDataConnection);
dataLink.setOnLinkChange(this, EVENT_LINK_STATE_CHANGED, null);
//某个条件触发执行 
trySetupData(String reason)
setupData(reason);
pdp = findFreePdp();
Message msg = obtainMessage();
msg.what = EVENT_DATA_SETUP_COMPLETE;
msg.obj = reason;
pdp.connect(apn, msg);
phone.mCM.setupDefaultPDP(apn.apn, apn.user, apn.password,
obtainMessage(EVENT_SETUP_PDP_DONE));
//收到EVENT_SETUP_PDP_DONE消息 
=>pdpconnection.java
handleMessage()
case EVENT_SETUP_PDP_DONE: 
	dataLink.connect();//dataLink是pppLink.java
	SystemService.start(SERVICE_PPPD_GPRS);//启动pppd_grps服务
	poll.what = EVENT_POLL_DATA_CONNECTION;
	sendMessageDelayed(poll, POLL_SYSFS_MILLIS);//启动轮询，看是否成功连接gprscheckPPP()//每隔5秒轮询，看是否连接成功，或断开
	//如果已经连接 
	mLinkChangeRegistrant.notifyResult(LinkState.LINK_UP);
//执行到pdpconnection.handleMessage() 
case EVENT_LINK_STATE_CHANGED:
	onLinkStateChanged(ls);
case LINK_UP: 
	notifySuccess(onConnectCompleted);
	onCompleted.sendToTarget();
//执行dataConnectionTracker.java的handleMessage() 
case EVENT_DATA_SETUP_COMPLETE:
	notifyDefaultData(reason);
	setupDnsProperties();
	setState(State.CONNECTED);
	phone.notifyDataConnection(reason);
	startNetStatPoll();
	resetPollStats();
```
1、读取发送出去的包数和接受到的包数 
2、如果发送的数据包且没有收到应答包数n大于等于看门狗追踪的限定包数。 
2.1、开始轮询pdp context list，尝试恢复网络连接 
2.2、如果轮询24次后还没有联通网络则停止网络状态轮询，进行一次ping实验。 
2.2.1、如果ping成功则，重新进行网络状态轮询，否则发送EVENT_START_RECOVERY事件。
// reset reconnect timer 
nextReconnectDelay = RECONNECT_DELAY_INITIAL_MILLIS; 
着重c++部分代码的角度分析
```  
＝>DataConnectionTracker.java
	trySetupData(String reason)
	setupData(reason);
＝>PdpConnection.java
	pdp.connect(apn, msg);
=>RIL.JAVA
	phone.mCM.setupDefaultPDP(apn.apn, apn.user, apn.password,
	obtainMessage(EVENT_SETUP_PDP_DONE));
	send(rr);
	//send socket to RIL 
	//enter c++ layer 
=>ril.cpp
	processCommandsCallback (int fd, short flags, void *param)
	processCommandBuffer(p_record, recordlen);
	status = p.readInt32(&request);
	pRI->pCI = &(s_commands[request]);
	pRI->pCI->dispatchFunction(p, pRI);
	dispatchStrings();
	s_callbacks.onRequest(pRI->pCI->requestNumber, pStrings, datalen, pRI);
=>reference-ril.c
	onRequest();
	requestSetupDefaultPDP(data, datalen, t);
	err = write_at_to_data_channel("ATD*99***1",1);
	//after a while.get "connect" from data channel,so need to send socket message to java layer. 
	p.writeInt32 (RESPONSE_SOLICITED);
	p.writeInt32 (pRI->token);//the serial No in the request list.
	errorOffset = p.dataPosition();
	p.writeInt32 (e);
	if (e == RIL_E_SUCCESS) { 
	/* process response on success */
	ret = pRI->pCI->responseFunction(p, response, responselen);
	/* if an error occurred, rewind and mark it */
	if (ret != 0) { 
	p.setDataPosition(errorOffset);
	p.writeInt32 (ret);
	} 
	} 
	sendResponse(p);
	sendResponseRaw(p.data(), p.dataSize());
	ret = blockingWrite(fd, (void *)&header, sizeof(header));
	blockingWrite(fd, data, dataSize);
=>RIL.JAVA
	RILReceiver.run();
	length = readRilMessage(is, buffer);
	p = Parcel.obtain();
	p.unmarshall(buffer, 0, length);
	p.setDataPosition(0);
	processResponse(p);
	processSolicited (p);
	serial = p.readInt();
	error = p.readInt();
	rr = findAndRemoveRequestFromList(serial);
	ret = responseStrings(p);
	if (rr.mResult != null) { 
		AsyncResult.forMessage(rr.mResult, ret, null);
		rr.mResult.sendToTarget();
	} 
=>pdpConnection.java
	handleMessage()
	case EVENT_SETUP_PDP_DONE: 
	...
	dataLink.connect();
=>pppLink.java
	SystemProperties.set(PROPERTY_PPPD_EXIT_CODE, "");
	SystemService.start(SERVICE_PPPD_GPRS);//启动pppd_grps服务
	poll.what = EVENT_POLL_DATA_CONNECTION;
	sendMessageDelayed(poll, POLL_SYSFS_MILLIS);
	dataConnection.state = State.CONNECTING;
	handleMessage()
	case EVENT_POLL_DATA_CONNECTION 
	checkPPP();
	if (ArrayUtils.equals(mCheckPPPBuffer, UP_ASCII_STRING, UP_ASCII_STRING.length) 
			|| ArrayUtils.equals(mCheckPPPBuffer, UNKNOWN_ASCII_STRING,
	UNKNOWN_ASCII_STRING.length) && dataConnection.state == State.CONNECTING)
	if (mLinkChangeRegistrant != null) { 
		mLinkChangeRegistrant.notifyResult(LinkState.LINK_UP);
	}
=>pdpConnection.java
	handleMessage()
	case EVENT_LINK_STATE_CHANGED: 
		DataLink.LinkState ls = (DataLink.LinkState) ar.result;
		onLinkStateChanged(ls);
	case LINK_UP: 
		notifySuccess(onConnectCompleted);
		AsyncResult.forMessage(onCompleted);
		onCompleted.sendToTarget();
=>DataConnectionTracker.java
	handleMessage()
	case EVENT_DATA_SETUP_COMPLETE: 
		... SystemProperties.set("gsm.defaultpdpcontext.active", "true");
		notifyDefaultData(reason);
		setupDnsProperties();
	//设置dns，gw，我们的实现方式是在pppd中设置的，不用pppd拨号的适用。 
	setState(State.CONNECTED);
	phone.notifyDataConnection(reason);
	mNotifier.notifyDataConnection(this, reason);
＝>DefaultPhoneNotifier.java
	mRegistry = ITelephonyRegistry.Stub.asInterface(ServiceManager.getService(
		"telephony.registry"));构造函数中初始化了mRegistry mRegistry.notifyDataConnection(convertDataState(sender.getDataConnectionState()), sender.isDataConnectivityPossible(), reason, sender.getActiveApn(), 
		sender.getInterfaceName(null));
		startNetStatPoll();
	} 
```