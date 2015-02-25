第五部分：通话相关的语音通路切换原理、震动接口 
5、语音通路 
5.1、设置语音通路的路由 
目前我们有两处处理: 
a、CallTracker.java中的handlePollCalls（）检测到＋clcc返回的电话列表中有状态为DriverCall.State.ALERTING(表示拨打电话后，对方已经振铃)，此时需要设置语音通路为MODE_IN_CALL 
b、PhoneUtils.java中setAudioMode（）函数 
c、调用通路分析 
```  
AudioManager audioManager = (AudioManager) context.getSystemService(Context.AUDIO_SERVICE);
audioManager.setMode(mode);
AudioManager.setMode(mode);
AudioService.setMode(mode);
AudioSystem.setMode(mode);//(native function)
android_media_AudioSystem.cpp==>android_media_AudioSystem_setMode()
AudioSystem.cpp==>setMode()
const sp<IAudioFlinger>& af = AudioSystem::get_audio_flinger();
binder = sm->getService(String16("media.audio_flinger"));
gAudioFlinger = interface_cast<IAudioFlinger>(binder);
return gAudioFlinger; 
```
通过查找“media.audio_flinger”发现AudioFlinger.cpp==>instantiate()//Main_mediaserver.cpp中被实例化。      
```  
defaultServiceManager()->addService(String16("media.audio_flinger"), new AudioFlinger());
mAudioHardware = AudioHardwareInterface::create();
LOGV("Creating Vendor Specific AudioHardware");
hw = createAudioHardware();
return new AudioHardwareMarvell(); 
return af->setMode(mode); 
AudioHardwareLittle.cpp==>setMode(mode)
doRouting();
enable_incall_headphone()//or others...
system("alsactl -f /etc/alsactl/asound.state_none restore");
system("alsactl -f /etc/alsactl/asound.state_headset_r_s restore");
```
5.2、来电播放振铃,挂断或接听停止振铃。 
```  
==>Phone.app
onCreate()
ringer = new Ringer(phone);
Vibrator mVibrator = new Vibrator();
mService = IHardwareService.Stub.asInterface(ServiceManager.getService("hardware"));
notifier = new CallNotifier(this, phone, ringer, mBtHandsfree);
mPhone.registerForIncomingRing(this, PHONE_INCOMING_RING, null);
mPhone.registerForPhoneStateChanged(this, PHONE_STATE_CHANGED, null);
mPhone.registerForDisconnect(this, PHONE_DISCONNECT, null);
...
case PHONE_INCOMING_RING: 
mRinger.ring();
mHardwareService.setAttentionLight(true);
mVibratorThread.start();
while (mContinueVibrating) { 
	mVibrator.vibrate(VIBRATE_LENGTH);
	SystemClock.sleep(VIBRATE_LENGTH + PAUSE_LENGTH);
} 
...
makeLooper();
mRingHandler.sendEmptyMessage(PLAY_RING_ONCE);
...
case PLAY_RING_ONCE: 
	PhoneUtils.setAudioMode(mContext,AudioManager.MODE_RINGTONE);
	r.play();
	...
case PHONE_DISCONNECT: 
case PHONE_STATE_CHANGED: 
	...
	mRinger.stopRing();
	Message msg = mRingHandler.obtainMessage(STOP_RING);
	msg.obj = mRingtone;
	mRingHandler.sendMessage(msg);
case STOP_RING: 
	r.stop();
	getLooper().quit();
	...mVibrator.cancel();
```
第六部分：通话相关的notification服务 
6、通话相关的notification服务。 
6.1、NotificationMgr 
==>PhoneApp.java 
onCreate() 
NotificationMgr.init(this)//NotificationMgr.java//此类主要负责电话通知的具体表现（通知和取消通知），未接图标、通话中、蓝牙激活中、保持中，静音、免提等。封装了简单的瞬间显示文本消息的功能。提供漫游数据连接禁止的通知封装和漫游数据连接允许时取消通知 
```  
sMe = new NotificationMgr(context); 
mNotificationMgr = (NotificationManager) 
context.getSystemService(Context.NOTIFICATION_SERVICE); 
mStatusBar = (StatusBarManager) context.getSystemService(Context.STATUS_BAR_SERVICE); //主要用于显示静音和
```
speaker状态的图表（在状态条右边显示） 
sMe.updateNotifications();//主要功能是： 
1、查询是否有未读的未接听电话，并显示到状态栏图标，和通知列表 
2、根据是否是电话状态，更新状态栏图表和通知列表（可能是激活，蓝牙，保持等） 
6.2、CallNotifier 
==>PhoneApp.java 
onCreate() 
notifier = new CallNotifier(this,phone,ringer,mBtHandsfree);//此类主要是监听通话相关的事件，然后进行例如来电播放铃声，震动。挂断、接听停止振铃等（调用Ringer类实现此功能)，根据不同的状态调用调用NotificationMgr进行具体的通知和取消通知。 