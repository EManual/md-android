下面来研究一个incoming call的流程：
1. 创建GsmPhone时，mCT = new GsmCallTracker(this);
2. 创建GsmCallTracker时:
```  
cm.registerForCallStateChanged(this, EVENT_CALL_STATE_CHANGE, null); -->    
mCallStateRegistrants.add(r); 
```
3. RIL中的RILReceiver线程首先读取从rild中传来的数据：processResponse -> processUnsolicited。
4. 对应于incoming call,RIL收到RIL_UNSOL_RESPONSE_CALL_STATE_CHANGED 消息，触发mCallStateRegistrants中的所有记录。
5. GsmCallTracker处理EVENT_CALL_STATE_CHANGE，调用pollCallsWhenSafe。
6. 函数pllCallsWhenSafe 处理：
```  
lastRelevantPoll = obtainMessage(EVENT_POLL_CALLS_RESULT);
cm.getCurrentCalls(lastRelevantPoll);
```
7. RIL::getCurrentCalls
```  
RILRequest rr = RILRequest.obtain(RIL_REQUEST_GET_CURRENT_CALLS, result);
...
send(rr);	
```
8. 接着RIL调用processSolicited处理RIL_REQUEST_GET_CURRENT_CALLS的返回结果。
9. GsmCallTracker的handleMessage被触发，处理事件EVENT_POLL_CALLS_RESULT，调用函数handlePollCalls
10.handlPollCalls 调用phone.notifyNewRingingConnection(newRinging);
11. PhoneApp中创建CallNotifier
12. CallNotifier注册：
```  
registerForNewRingingConnection -> mNewRingingConnectionRegistrants.addUnique(h, what, obj);
```