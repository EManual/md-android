通过程序获取android系统手机的铃声和音量。同样，设置铃声和音量的方法也很简单！ 
```  
AudioManager mAudioManager = (AudioManager) getSystemService(Context.AUDIO_SERVICE);
```
设置音量的方法也很简单，AudioManager提供了方法： 
```  
public void setStreamVolume(int streamType, int index, int flags)
```
streamType为铃声类型，例如：AudioManager.STREAM_VOICE_CALL、AudioManager.STREAM_SYSTEM等，index为音量大小，falgs为标志位。
设置振动：
```  
mVibrator = (Vibrator) mContext.getSystemService(Service.VIBRATOR_SERVICE);   
long[] pattern = {150, 100}; // OFF/ON/OFF/ON...  
mVibrator.vibrate(pattern, -1);
```
静音：
设置系统声音为0就行
```  
//通话时设置静音System.out.println("isMicrophoneMute =" + audioManager.isMicrophoneMute());
audioManager.setMicrophoneMute(!audioManager.isMicrophoneMute());
//通话时设置免提System.out.println("isSpeakerphoneOn =" + audioManager.isSpeakerphoneOn());
audioManager.setSpeakerphoneOn(!audioManager.isSpeakerphoneOn());
```
别忘了修改的权限：
```  
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
```
Android开发_如何获取和设置android系统铃声和音量大小
通过程序获取android系统手机的铃声和音量。同样，设置铃声和音量的方法也很简单！
设置音量的方法也很简单，AudioManager提供了方法：
```  
public void setStreamVolume(int streamType,int index,int flags)
```
其中streamType有内置的常量，去文档里面就可以看到。
JAVA代码：
```  
AudioManager mAudioManager = (AudioManager) getSystemService(Context.AUDIO_SERVICE);
//通话音量
int max = mAudioManager.getStreamMaxVolume( AudioManager.STREAM_VOICE_CALL );
int current = mAudioManager.getStreamVolume( AudioManager.STREAM_VOICE_CALL );
Log.d("VIOCE_CALL", "max : " + max + " current : " + current);
//系统音量
max = mAudioManager.getStreamMaxVolume( AudioManager.STREAM_SYSTEM );
current = mAudioManager.getStreamVolume( AudioManager.STREAM_SYSTEM );
Log.d("SYSTEM", "max : " + max + " current : " + current);
//铃声音量
max = mAudioManager.getStreamMaxVolume( AudioManager.STREAM_RING );
current = mAudioManager.getStreamVolume( AudioManager.STREAM_RING );
Log.d("RING", "max : " + max + " current : " + current);
//音乐音量
max = mAudioManager.getStreamMaxVolume( AudioManager.STREAM_MUSIC );
current = mAudioManager.getStreamVolume( AudioManager.STREAM_MUSIC );
Log.d("MUSIC", "max : " + max + " current : " + current);
//提示声音音量
max = mAudioManager.getStreamMaxVolume( AudioManager.STREAM_ALARM );
current = mAudioManager.getStreamVolume( AudioManager.STREAM_ALARM );
Log.d("ALARM", "max : " + max + " current : " + current);
```
ps：游戏过程中只允许调整多媒体音量，而不允许调整通话音量。
```  
setVolumeControlStream(AudioManager.STREAM_MUSIC);
```
长时间不动，不允许黑屏，View.setKeepScreenOn(true);