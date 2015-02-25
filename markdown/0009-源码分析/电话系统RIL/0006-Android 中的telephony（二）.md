函数readerLoop运行在一个单独的读线程里面，负责从modem中读取数据。读到的数据可分为三种类型：网络端传入的事件；modem对当前AT command的部分响应；modem对当前AT command的全部响应。对第三种类型的数据(AT command的全部响应），读线程唤醒(pthread_cond_signal）睡眠状态的主线程。
#### 第二部分 Java代码
Android中，telephony相关的java代码主要在下列目录中：
1. frameworks/base/telephony/java/android/telephony
2. frameworks/base/telephony/java/com/android/internal/telephony
3. frameworks/base/services/java/com/android/server/TelephonyRegistry.java
4. packages/apps/Phone
其中，目录1中的代码提供Android telephony的公开接口，任何具有权限的第三方应用都可使用，如接口类TelephonyManager。
目录2/3中的代码提供一系列内部接口，目前第三方应用还不能使用。当前似乎只有packages/apps/Phone能够使用
目录4是一个特殊应用，或者理解为一个平台内部进程。其他应用通过intent方式调用这个进程的服务。
TelephonyManager主要使用两个服务来访问telephony功能：
1.ITelephony，提供与telephony 进行操作，交互的接口，在packages/apps/Phone中由PhoneInterfaceManager.java实现。
2.ITelephonyRegistry,提供登记telephony事件的接口。由frameworks/base/services/java/com/android/server/TelephonyRegistry.java实现。
interface CommandsInterface 描述了对电话的所有操作接口，如命令， 查询状态，以及电话事件监听等
class BaseCommands是CommandsInterface的直接派生类，实现了电话事件的处理(发送message给对应的handler)。
而class RIL又派生自BaseCommands。RIL负责实际实现CommandsInterface中的接口方法。RIL通过Socket和rild守护进程进行通讯。对于每一个命令接口方法，如acceptCall，或者状态查询，将它转换成对应的RIL_REQUEST_XXX，发送给rild。线程RILReceiver监听socket,当有数据上报时，读取该数据并处理。读取的数据有两种，
1. 电话事件，RIL_UNSOL_xxx, RIL读取相应数据后，发送message给对应的handler (详见函数processUnsolicited)。
2. 命令的异步响应。(详见函数processSolicited)。
interface Phone描述了对电话的所有操作接口。 
PhoneBase直接从Phone派生而来。而另外两个类，CDMAPhone和GSMPhone，又从PhoneBase派生而来，分别代表对CDMA和GSM的操作。
PhoneProxy也从Phone直接派生而来。当当前不需要区分具体是CDMA Phone还是GSM Phone时，可使用PhoneProxy。
抽象类Call代表一个call，有两个派生类CdmaCall和GsmCall。
interface PhoneNotifier定义电话事件的通知方法DefaultPhoneNotifier从PhoneNotifier派生而来。在其方法实现中，通过调用service ITelephonyRegistry来发布电话事件。
service ITelephonyRegistey由frameworks/base/services/java/com/android/server/TelephonyRegistry.java实现。这个类通过广播intent，从而触发对应的broadcast receiver。
在PhoneApp创建时，
sPhoneNotifier = new DefaultPhoneNotifier();
...
sCommandsInterface = new RIL(context, networkMode, cdmaSubscription);
然后根据当前phone是cdma还是gsm,创建对应的phone，如
```  
sProxyPhone = new PhoneProxy(new GSMPhone(context,sCommandsInterface, sPhoneNotifier));
```
下面我们来研究一个电话打出去的流程。
```  
TwelveKeyDialer.java, onKeyUp()
TwelveKeyDialer.java, placeCall()
OutgoingCallBroadcaster.java, onCreate()
sendOrderedBroadcast(broadcastIntent, PERMISSION,
new OutgoingCallReceiver(), null, Activity.RESULT_OK, number, null);
OutgoingCallBroadcaster.java, OutgoingCallReceiver
doReceive -> context.startActivity(newIntent);
InCallScreen.java, onCreate/onNewIntent
InCallScreen.java, placeCall
PhoneUtils.java, placeCall
GSMPhone.java, dial
GsmCallTracker.java, dial
RIL.java, dial
RILRequest rr = RILRequest.obtain(RIL_REQUEST_DIAL, result);
...
send(rr);
```