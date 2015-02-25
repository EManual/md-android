AudioManage 管理控制声音
#### 继承关系
```  
public class AudioManager extends Object      
java.lang.Object
```
android.media.AudioManager
#### 类概述
AudioManager类提供访问音量和振铃模式的控制。
用Context.getSystemService(Context.AUDIO_SERVICE)得到这个类的实例。
常量
```  
public static final String ACTION_AUDIO_BECOMING_NOISY
```
广播intent，提示应用程序音频信号由于音频输出的变化将变得“嘈杂”。例如，当拔出一个有线耳机，或断开一个支持A2DP的音频接收器，这个intent就会被发送，且音频系统将自动切换音频线路到扬声器。收到这个intent后，控制音频流的应用程序会考虑暂停，减小音量或其他措施，以免扬声器的声音使用户惊奇。
常量值："android.media.AUDIO_BECOMING_NOISY"
```  
public static final String ACTION_SCO_AUDIO_STATE_CHANGED
```
广播intent，表明蓝牙SCO音频连接状态已改变。这个intent包含额外信息：EXTRA_SCO_AUDIO_STATE，它表明新的状态是SCO_AUDIO_STATE_DISCONNECTED或SCO_AUDIO_STATE_CONNECTED。
参照
```  
startBluetoothSco()
```
常量值："android.media.SCO_AUDIO_STATE_CHANGED"
```  
public static final int ADJUST_LOWER
```
减小铃声音量。
参照
```  
adjustVolume(int, int)
adjustStreamVolume(int, int, int)
```
常量值： -1 (0xffffffff) 
```  
public static final int ADJUST_RAISE
```
增大铃声音量。
参照
```  
adjustVolume(int, int)
adjustStreamVolume(int, int, int)
```
常量值： 1 (0x00000001)
```  
public static final int ADJUST_SAME
```
保持先前的铃声音量。当需要toast显示音量而不修改它时可能是有用的。
参照
```  
adjustVolume(int, int)
adjustStreamVolume(int, int, int)
```
常量值： 0 (0x00000000) 
```  
public static final int AUDIOFOCUS_GAIN
```
用来指示获得音频焦点，请求音频焦点，未知持续时间
参照
```  
onAudioFocusChange(int)
requestAudioFocus(OnAudioFocusChangeListener, int, int)
```
常量值： 1 (0x00000001) 
```  
public static final int AUDIOFOCUS_GAIN_TRANSIENT
```
用来指示临时获得或请求音频焦点，预期持续很短的时间。临时改变的例子是操纵方向的回放，或一个事件的通知。
参照
```  
onAudioFocusChange(int)
requestAudioFocus(OnAudioFocusChangeListener, int, int)
```
常量值： 2 (0x00000002) 
```  
public static final int AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK
```
用来指示一个临时的音频焦点请求，预期持续很短的时间，其它音频程序降低他们的输出级别后保持播放是可接受的（也被称为“ducking”）。临时改变的例子是操纵方向的回入，在后台回放音乐是可接受的。
参照
```  
onAudioFocusChange(int)
requestAudioFocus(OnAudioFocusChangeListener, int, int)
```
常量值： 3 (0x00000003)
```  
public static final int AUDIOFOCUS_LOSS
```
用来指示在未知持续时间内丢失音频焦点
参照
```  
onAudioFocusChange(int)
```
常量值： -1 (0xffffffff)
```  
public static final int AUDIOFOCUS_LOSS_TRANSIENT
```
用来指示暂时的丢失音频焦点。
参照
```  
onAudioFocusChange(int)
```
常量值： -2 (0xfffffffe) 
```  
public static final int AUDIOFOCUS_LOSS_TRANSIENT_CAN_DUCK
```
用来指示暂时的丢失音频焦点，若音频焦点的丢失者想继续播放，它会降低自己的输出
音量（也被称为“ducking”），因为新的焦点拥有者不要求其它静音。
参照
```  
onAudioFocusChange(int)
```
常量值： -3 (0xfffffffd) 
```  
public static final int AUDIOFOCUS_REQUEST_FAILED
```
一个失败的焦点转移请求。
常量值： 0 (0x00000000) 
```  
public static final int AUDIOFOCUS_REQUEST_GRANTED
```
一个成功的焦点转移请求。
常量值： 1 (0x00000001) 
```  
public static final String EXTRA_RINGER_MODE
```
新的振铃模式。
参照
```  
RINGER_MODE_CHANGED_ACTION
RINGER_MODE_NORMAL
RINGER_MODE_SILENT
RINGER_MODE_VIBRATE
```
常量值： "android.media.EXTRA_RINGER_MODE" 
```  
public static final String EXTRA_SCO_AUDIO_STATE
```
额外的intent ACTION_SCO_AUDIO_STATE_CHANGED包含新的蓝牙SCO连接状态。
常量值： "android.media.extra.SCO_AUDIO_STATE" 
```  
public static final String EXTRA_VIBRATE_SETTING
```
特定类型的新振动设置。
参照
```  
VIBRATE_SETTING_CHANGED_ACTION
EXTRA_VIBRATE_TYPE
VIBRATE_SETTING_ON
VIBRATE_SETTING_OFF
VIBRATE_SETTING_ONLY_SILENT
```
常量值： "android.media.EXTRA_VIBRATE_SETTING" 
```  
public static final String EXTRA_VIBRATE_TYPE
```
振动类型设置发生变化。
参照
```  
VIBRATE_SETTING_CHANGED_ACTION
VIBRATE_TYPE_NOTIFICATION
VIBRATE_TYPE_RINGER
```
常量值： "android.media.EXTRA_VIBRATE_TYPE" 
```  
public static final int FLAG_ALLOW_RINGER_MODES
```
当改变音量时，是否将振铃模式作为候选项。例如，如果为true且音量级别为0音量或用ADJUST_LOWER调整音量，那么振铃模式会改变静音或振动模式。
对铃声流来说这个选项默认是打开的。如果包括这个标志，这个行为将被展示，不管流类型受振铃模式的影响。
参照
```  
adjustVolume(int, int)
adjustStreamVolume(int, int, int)
```
常量值： 2 (0x00000002)
```  
public static final int FLAG_PLAY_SOUND
```
当改变音量时，是否播放声音。
若由adjustVolume(int,int)或adjustSuggestedStreamVolume(int,int,int)给出，则在某些情况下会被忽略（例如，采用的流类型不是STREAM_RING，或正在向下调整音量）。
参照
```  
adjustStreamVolume(int, int, int)
adjustVolume(int, int)
setStreamVolume(int, int, int)
```
常量值： 4 (0x00000004) 
```  
public static final int FLAG_REMOVE_SOUND_AND_VIBRATE
```
移除队列中或正在播放（与改变音量相关）的声音/振动。
常量值： 8 (0x00000008) 
```  
public static final int FLAG_SHOW_UI
```
显示包含当前音量的toast。
参照
```  
adjustStreamVolume(int, int, int)
adjustVolume(int, int)
setStreamVolume(int, int, int)
setRingerMode(int)
```
常量值： 1 (0x00000001) 
```  
public static final int FLAG_VIBRATE
```
若进入振动铃声模式是否振动。
常量值： 16 (0x00000010) 
```  
public static final int FX_FOCUS_NAVIGATION_DOWN
```
焦点下移。
参照
```  
playSoundEffect(int)
```
常量值： 2 (0x00000002)
```  
public static final int FX_FOCUS_NAVIGATION_LEFT
```
焦点左移
参照
```  
playSoundEffect(int)
```
常量值： 3 (0x00000003) 
```  
public static final int FX_FOCUS_NAVIGATION_RIGHT
```
焦点右移。
参照
```  
playSoundEffect(int)
```
常量值： 4 (0x00000004) 
```  
public static final int FX_FOCUS_NAVIGATION_UP
```
焦点上移。
参照
```  
playSoundEffect(int)
```
常量值： 1 (0x00000001) 
```  
public static final int FX_KEYPRESS_DELETE
```
IME删除按键声音。
参照
```  
playSoundEffect(int)
```
常量值： 7 (0x00000007) 
```  
public static final int FX_KEYPRESS_RETURN
```
IME返回按键声音。
参照
```  
playSoundEffect(int)
```
常量值： 8 (0x00000008) 
```  
public static final int FX_KEYPRESS_SPACEBAR
```
IME空格按键声音。
参照
```  
playSoundEffect(int)
```
常量值： 6 (0x00000006) 
```  
public static final int FX_KEYPRESS_STANDARD
```
IME标准按键声音。
参照
```  
playSoundEffect(int)
```
常量值： 5 (0x00000005) 
```  
public static final int FX_KEY_CLICK
```
键盘和方向键点击声音。
参照
```  
playSoundEffect(int)
```
常量值： 0 (0x00000000) 
```  
public static final int MODE_CURRENT
```
当前音频模式。用来请求音频路由到当前模式。
常量值： -1 (0xffffffff) 
```  
public static final int MODE_INVALID
```
无效的音频模式
常量值： -2 (0xfffffffe) 
```  
public static final int MODE_IN_CALL
```
呼叫的音频模式。建立一个电话呼叫。
常量值： 2 (0x00000002) 
```  
public static final int MODE_IN_COMMUNICATION
```
通信的音频模式。建立一个音频/视频或VoIP呼叫。
常量值： 3 (0x00000003) 
```  
public static final int MODE_NORMAL
```
正常的音频模式：不是振铃或建立呼叫。
常量值： 0 (0x00000000) 
```  
public static final int MODE_RINGTONE
```
振铃的音频模式。获取信号输入。
常量值： 1 (0x00000001)
```  
public static final int NUM_STREAMS
```
已弃用。
用 AudioSystem.getNumStreamTypes() 代替。
常量值： 5 (0x00000005) 
```  
public static final String RINGER_MODE_CHANGED_ACTION
```
广播intent表明振铃模式被改变。包括新的振铃模式。
参照
```  
EXTRA_RINGER_MODE
```
常量值： "android.media.RINGER_MODE_CHANGED" 
```  
public static final int RINGER_MODE_NORMAL
```
振铃模式可以是听得见的或振动的。若音量在改变这一模式前是听得见的，则它是听得见的。若打开振动设置，则它是振动的。
参照
```  
setRingerMode(int)
getRingerMode()
```
常量值： 2 (0x00000002) 
```  
public static final int RINGER_MODE_SILENT
```
振铃模式是静音且不振动。（这要覆盖振动设置。）
参照
```  
setRingerMode(int)
getRingerMode()
```
常量值： 0 (0x00000000)
```  
public static final int RINGER_MODE_VIBRATE      
```
振铃模式是静音且振动。（这会导致电话铃声一直振动，但若设置当通知振动时振动）
参照
```  
setRingerMode(int)
getRingerMode()
```
常量值： 1 (0x00000001) 
```  
public static final int ROUTE_ALL
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
用来屏蔽setRouting(int, int, int)的参数。
常量值： -1 (0xffffffff) 
```  
public static final int ROUTE_BLUETOOTH
```
已弃用。
用ROUTE_BLUETOOTH_SCO。不要直接设置音频路由，用方法setSpeakerphoneOn(), 
setBluetoothScoOn()代替。
常量值： 4 (0x00000004)
```  
public static final int ROUTE_BLUETOOTH_A2DP
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
路由音频输出到蓝牙A2DP。
常量值： 16 (0x00000010)
```  
public static final int ROUTE_BLUETOOTH_SCO
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
路由音频输出到蓝牙SCO。
常量值： 4 (0x00000 
```  
public static final int ROUTE_EARPIECE
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
路由音频输出到听筒。
常量值： 1 (0x00000001) 
```  
public static final int ROUTE_HEADSET
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
路由音频输出到耳机。
常量值： 8 (0x00000008) 
```  
public static final int ROUTE_SPEAKER
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
路由音频输出到扬声器。
常量值： 2 (0x00000002) 
```  
public static final int SCO_AUDIO_STATE_CONNECTED
```
EXTRA_SCO_AUDIO_STATE的额外值表明SCO音频通道已建立。
常量值： 1 (0x00000001) 
```  
public static final int SCO_AUDIO_STATE_DISCONNECTED
```
EXTRA_SCO_AUDIO_STATE的额外值表明SCO音频通道未建立。
常量值： 0 (0x00000000)
```  
public static final int SCO_AUDIO_STATE_ERROR
```
EXTRA_SCO_AUDIO_STATE的额外值表明尝试获取状态有错误。
常量值： -1 (0xffffffff) 
```  
public static final int STREAM_ALARM
```
用于报警的音频流。
常量值： 4 (0x00000004)
```  
public static final int STREAM_DTMF
```
用于DTMF Tones的音频流。
常量值： 8 (0x00000008) 
```  
public static final int STREAM_MUSIC
```
用于音乐回放的音频流。
常量值： 3 (0x00000003) 
```  
public static final int STREAM_NOTIFICATION
```
用于通知的音频流。
常量值： 5 (0x00000005) 
```  
public static final int STREAM_RING
```
用于电话铃声的音频流。
常量值： 2 (0x00000002) 
```  
public static final int STREAM_SYSTEM
```
用于系统声音的音频流。
常量值： 1 (0x00000001) 
```  
public static final int STREAM_VOICE_CALL
```
用于电话的音频流。
常量值： 0 (0x00000000) 
```  
public static final int USE_DEFAULT_STREAM_TYPE
```
建议使用默认的流类型。这可能不适用于所有需要流类型的地方。
常量值： -2147483648 (0x80000000) 
```  
public static final String VIBRATE_SETTING_CHANGED_ACTION
```
广播intent，表明振动设置已改变。包括振动类型和其新设置。
参照
```  
EXTRA_VIBRATE_TYPE
EXTRA_VIBRATE_SETTING
```
常量值： "android.media.VIBRATE_SETTING_CHANGED" 
```  
public static final int VIBRATE_SETTING_OFF
```
建议不振动的振动设置
参照
```  
setVibrateSetting(int, int)
getVibrateSetting(int)
```
常量值： 0 (0x00000000) 
```  
public static final int VIBRATE_SETTING_ON
```
建议在合适时振动的振动设置。
参照
```  
setVibrateSetting(int, int)
getVibrateSetting(int)
```
常量值： 1 (0x00000001)
```  
public static final int VIBRATE_SETTING_ONLY_SILENT
```
建议只在振动铃声模式中振动的振动设置。
参照
```  
setVibrateSetting(int, int)
getVibrateSetting(int)
```
量值： 2 (0x00000002)
```  
public static final int VIBRATE_TYPE_NOTIFICATION
```
与通知相符的振动类型。
参照
```  
setVibrateSetting(int, int)
getVibrateSetting(int)
shouldVibrate(int)
```
常量值： 1 (0x00000001)
```  
public static final int VIBRATE_TYPE_RINGER
```
与振铃相符的振动类型。
参照
```  
setVibrateSetting(int, int)
getVibrateSetting(int)
shouldVibrate(int)
```
常量值： 0 (0x00000000)
公共方法
```  
public int abandonAudioFocus (AudioManager.OnAudioFocusChangeListener l) 
```
放弃音频焦点。若存在先前焦点拥有者，则使它接收焦点。
参数
I 请求焦点的监听器。 
返回值
AUDIOFOCUS_REQUEST_FAILED或 AUDIOFOCUS_REQUEST_GRANTED 
```  
public void adjustStreamVolume (int streamType, int direction, int flags) 
```
通过同一方向的一步调整特定流的音量。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
```  
streamType      欲调整的流类型。 值为STREAM_VOICE_CALL, STREAM_SYSTEM, 
STREAM_RING, STREAM_MUSIC或STREAM_ALARM。 
Direction  欲调整音量的方向。值为ADJUST_LOWER, ADJUST_RAISE,或ADJUST_SAME. 
Flags一个或多个标志。 
```
参照
```  
adjustVolume(int, int)
setStreamVolume(int, int, int)
public void adjustSuggestedStreamVolume (int direction, int suggestedStreamType, int flags) 
```
调整最相关流或给定的回放流的音量。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
```  
direction  欲调整音量的方向。 值为 ADJUST_LOWER, ADJUST_RAISE, 或 ADJUST_SAME. 
suggestedStreamType     若不存在相关流，则用该流类型。
USE_DEFAULT_STREAM_TYPE在这是有效的。
Flags 一个或多个标志。
```
参照
```  
adjustVolume(int, int)
adjustStreamVolume(int, int, int)
setStreamVolume(int, int, int)
public void adjustVolume (int direction, int flags) 
```
调整最相关流的音量。例如，若在通话中，不管通话屏幕是否显示，它都将拥有最高的优先级。另一个例子，若音乐正在后台播放且不在通话中，刚将调整音频流。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
```  
direction  欲调整音量的方向。 值为ADJUST_LOWER, ADJUST_RAISE, 或ADJUST_SAME. 
flags 一个或多个标志。
```
参照
```  
adjustSuggestedStreamVolume(int, int, int)
adjustStreamVolume(int, int, int)
setStreamVolume(int, int, int) 
public int getMode () 
```
返回当前的音频模式。
返回值
当前音频模式(MODE_NORMAL, MODE_RINGTONE, MODE_IN_CALL 或MODE_IN_COMMUNICATION). 从HAL中返回当前音频状态。  
```  
public String getParameters (String keys) 
```
获得音频硬件设置的参数值变量。
参数
keys 参数列表
返回值
键值对形式的参数列表： key1=value1;key2=value2;...  
```  
public int getRingerMode () 
```
返回当前铃声模式。
返回值
当前铃声模式，值为RINGER_MODE_NORMAL, RINGER_MODE_SILENT, 或RINGER_MODE_VIBRATE.
参照
setRingerMode(int)
```  
public int getRouting (int mode) 
```
已弃用。
不要直接查询音频路由，用方法isSpeakerphoneOn(), isBluetoothScoOn(), isBluetoothA2dpOn() and isWiredHeadsetOn()代替。
返回指定模式的当前音频路由位向量。
参数
mode 获取路由的音频模式 (e.g., MODE_RINGTONE) 
返回值
可以与ROUTE_xxx相比较的一个音频路由位向量
```  
public int getStreamMaxVolume (int streamType) 
```
返回特定流的最大音量索引。 
参数
streamType 返回最大音量索引的流类型。 
返回值
流的最大有效音量索引。
参照
getStreamVolume(int)
```  
public int getStreamVolume (int streamType) 
```
返回特定类的当前音量索引。
参数
streamType 返回音量索引的流类型。
返回值
The current volume index for the stream.
流的当前音量索引。
参照
getStreamMaxVolume(int)
setStreamVolume(int, int, int) 
```  
public int getVibrateSetting (int vibrateType) 
```
返回振动类型对应的用户振动设置。
大多数需要振动的客户端不应该使用这个方法，用shouldVibrate(int)代替。
参数
vibrateType 振动类型。值为VIBRATE_TYPE_NOTIFICATION或VIBRATE_TYPE_RINGER。
返回值
振动设置，值为VIBRATE_SETTING_ON, VIBRATE_SETTING_OFF, 或VIBRATE_SETTING_ONLY_SILENT.
参照
setVibrateSetting(int, int)
shouldVibrate(int) 
```  
public boolean isBluetoothA2dpOn () 
```
检查A2DP音频路由到蓝牙耳机是否打开。
返回值
true if A2DP audio is being routed to/from Bluetooth headset; false if otherwise 
若A2DP音频被路由到/从蓝牙耳机，返回ture。反之，返回false。 
```  
public boolean isBluetoothScoAvailableOffCall () 
```
表明当前平台是否支持使用SCO关闭通话用例。当电话不在通话中，应用程序需要使用蓝牙SCO音频，必须先调用这个方法以确保平台支持这一特性。
返回值
true if bluetooth SCO can be used for audio when not in call false otherwise
若当不在通话中时音频可以使用蓝牙SCO，返回ture。反之，返回false。
参照
startBluetoothSco() 
```  
public boolean isBluetoothScoOn () 
```
检查通信是否使用蓝牙SCO。
返回值
若通信使用SCO,返回true。反之，返回false。

public boolean isMicrophoneMute () 
```
检查麦克风是否静音。
返回值
若麦克风静音，返回true。反之，返回false。
```  
public boolean isMusicActive () 
```
检查是否有音乐是活动的。
返回值
若有音乐曲目是活动的，返回true。
```  
public boolean isSpeakerphoneOn () 
```
检查喇叭扩音器是否开着。
返回值
若喇叭扩音器开着，返回true。反之，返回false。
```  
public boolean isWiredHeadsetOn () 
```
检查音频路由到有线耳机是否开着。
返回值
若音频被路由到/从有线耳机，返回true。反之，返回false。
```  
public void loadSoundEffects () 
```
加载音效。当音效可用时，要先调用这个方法。
```  
public void playSoundEffect (int effectType, float volume) 
```
播放音效（按键声音，打开/关闭盖子）
参数
effectType  音效的类型。值为FX_KEY_CLICK, FX_FOCUS_NAVIGATION_UP, 
FX_FOCUS_NAVIGATION_DOWN, FX_FOCUS_NAVIGATION_LEFT, 
FX_FOCUS_NAVIGATION_RIGHT,FX_KEYPRESS_STANDARD, 
FX_KEYPRESS_SPACEBAR,FX_KEYPRESS_DELETE, 
FX_KEYPRESS_RETURN。
Volume 音效的音量。 音量值是原始标量so UI controls should be scaled logarithmically. 若音量指定为-1，用AudioManager.STREAM_MUSIC 流音量减去3dB。注意：这个版本应用于启动和控制音量面板的设置。
```  
public void playSoundEffect (int effectType) 
```
播放音效（按键声音，打开/关闭盖子）
参数
effectType 音效的类型。值为FX_KEY_CLICK, FX_FOCUS_NAVIGATION_UP, 
FX_FOCUS_NAVIGATION_DOWN, FX_FOCUS_NAVIGATION_LEFT, 
FX_FOCUS_NAVIGATION_RIGHT, FX_KEYPRESS_STANDARD,
FX_KEYPRESS_SPACEBAR, FX_KEYPRESS_DELETE, 
FX_KEYPRESS_RETURN,注意：这个版本使用UI设置决定声音能否
被听见。
```  
public void registerMediaButtonEventReceiver (ComponentName eventReceiver) 
```
注册一个组件，它是MEDIA_BUTTON intent的唯一接收器。
参数
eventReceiver 接收媒体按钮intent的BroadcastReceiver 的标志符。这个广播
接收器必须在应用程序清单中声明。
```  
public int requestAudioFocus (AudioManager.OnAudioFocusChangeListener l, int streamType, int durationHint) 
```
请求音频焦点。发送请求以获得音频焦点。
参数
l音频焦点改变时通知的接收器。
streamType 受焦点请求影响的主要音频流类型。
durationHint用AUDIOFOCUS_GAIN_TRANSIENT表示这个焦点请求是暂时的，很快会放弃这个焦点。暂时请求的例子有操纵方向的回放，通知声音。用AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK表示若先前焦点拥有者躲开它的音频输出，它保持播放是可行的。AUDIOFOCUS_GAIN用于一个求和持续时间的焦点请求，如回放歌曲或音频。
返回值
AUDIOFOCUS_REQUEST_FAILED或 AUDIOFOCUS_REQUEST_GRANTED  
```  
public void setBluetoothA2dpOn (boolean on) 
```
已弃用。
不要使用。
参数
on 若为true，路由A2DP音频到/从蓝牙耳机；若为false，禁用A2DP音频。 
```  
public void setBluetoothScoOn (boolean on) 
```
请求在通信中使用蓝牙SCO耳机。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
on 若为true，在通信中使用蓝牙SCO；若为false，不使用。
```  
public void setMicrophoneMute (boolean on) 
```
设置麦克风是否静音。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
on 若为ture，麦克风静音。若为flase，不静音。 
```  
public void setMode (int mode) 
```
设置音频模式。
音频模式包含音频路由和电话层的行为。因此，这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。特别地，MODE_IN_CALL模式只能用在当打电话时的电话程序中，因为它会引起信号从音频层馈入到平台混音器。
参数
mode 请求的音频模式(MODE_NORMAL, MODE_RINGTONE, MODE_IN_CALL 或MODE_IN_COMMUNICATION)。通知HAL当前的音频状态以便它能
适当的路由音频。
```  
public void setParameters (String keyValuePairs) 
```
给音频硬件设置一个可变数目的参数值。
参数
keyValuePairs 键值对形式的参数列表：key1=value1;key2=value2;... 
```  
public void setRingerMode (int ringerMode) 
```
设置铃声模式。
静音模式会静音且不会振动。振动模式会静音且会振动。正常模式是听得见的且根据用户设置决定是否振动。
参数
ringerMode   铃声模式。值为RINGER_MODE_NORMAL, RINGER_MODE_SILENT, or RINGER_MODE_VIBRATE.。
参照
getRingerMode() 
```  
public void setRouting (int mode, int routes, int mask) 
```
已弃用。
不要直接设置音频路由，用方法setSpeakerphoneOn(), setBluetoothScoOn()代替。
为特定模式设置音频路由。
参数
mode 改变路由的音频模式。E.g., MODE_RINGTONE. 
routes 请求路由的位向量，由一个或多个ROUTE_xxx类型创建。设置位表
明路由应该打开。 
mask改变路由位向量由一个或多个ROUTE_xxx类型创建复原位表明路由
应该不改变。 
public void setSpeakerphoneOn (boolean on) 
```
设置喇叭扩音器打开或关闭。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
on 为true打开喇叭扩音器；为false关闭喇叭扩音器。
```  
public void setStreamMute (int streamType, boolean state) 
```
静音或不静音音频流。
静音命令被保护以免客户端进程死亡：若具有流上的活动静音请求的进程死亡，这个流会自动取消静音。
对于给定的流，静音请求是累计的：AudioManager会从一个或多个客户端接收数个静音请求，只有当接收到相同数目的取消静音请求时流才会取消静音。
为了更好的用户体验，应该程序必须在onPause()中取消已静音流，若合适在onResume()中再次静音
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
streamType 欲静音/取消静音的流。
state请求静音状态：若为true，静音；若为false，取消静音。
```  
public void setStreamSolo (int streamType, boolean state) 
```
单独的或不单独的一个特定类。其它流静音。
保护solo命令以免客户端进程死亡：若有在流上活动的solo请求的进程死亡，所有的流都被静音，因为这一请求被自动取消静音。
对给定类的solo请求是累计的：AudioManager会从一个或多个客户端接收数个solo请求，只有当接收到相同数目的unsoloed请求时流才会是unsoloed。
为了更好的用户体验，应该程序必须在onPause()中unsole a soloed 流，若合适在onResume()中再次solo。
参数
streamType  soloed/unsolod的流。
state 请求的solo状态：solo打开为true，关闭为false。 
```  
public void setStreamVolume (int streamType, int index, int flags) 
```
设置特定流的音量索引。
参数
streamType 欲设置音量索引的流。 
index欲设置的音量索引。参照getStreamMaxVolume(int) 获得最大有效值。
flags 一个或多个标志。
参照
getStreamMaxVolume(int)
getStreamVolume(int) 
```  
public void setVibrateSetting (int vibrateType, int vibrateSetting) 
```
当振动类型应该振动时，设置配置。
这个方法只能用于代替音频设置的平台范围管理应用程序或主要电话应用程序。
参数
vibrateType 振动类型。值为VIBRATE_TYPE_NOTIFICATION 或VIBRATE_TYPE_RINGER。
vibrateSetting 振动设置。值为VIBRATE_SETTING_ON, VIBRATE_SETTING_OFF,或VIBRATE_SETTING_ONLY_SILENT。
参照
getVibrateSetting(int)
shouldVibrate(int) 
```  
public void setWiredHeadsetOn (boolean on) 
```
已弃用。
不再使用。
设置打开或关闭音频路由到有线耳机。
参数
on 为true，设置路由音频到/从有线耳机；为false， 禁用有线耳机音频。 
```  
public boolean shouldVibrate (int vibrateType) 
```
根据用户的设置和当前铃声模式，返回一个特定类型是否应该振动。
使用通知来振动的大多数客户端不应该使用这个方法。若策略不允许，通知管理器不会振动，故客户端常常设置振动模式且让通知管理器控制是否振动。
参数
vibrateType 振动类型。值为VIBRATE_TYPE_NOTIFICATION或VIBRATE_TYPE_RINGER。
返回值
在调用这个方法的瞬间，类型是否应该振动。
参照
setVibrateSetting(int, int)
getVibrateSetting(int)
```  
public void startBluetoothSco () 
```
启动蓝牙SCO音频连接。
需要权限：MODIFY_AUDIO_SETTINGS。
当电话不在通话中，想发送/接收音频到/从蓝牙SCO耳机的应用程序可以使用这个方法。
由于SCO连接会花费几秒钟，应用程序不应该依赖方法返回的可用连接，而是注册接收intent.ACTION_SCO_AUDIO_STATE_CHANGED，并等待状态变为SCO_AUDIO_STATE_CONNECTED。
由于不保证连接成功，应用程序必须等待这个intent超时。
当完成SCO连接或建立超时，应用程序必须调用stopBluetoothSco()去清空请求并关闭蓝牙连接。
即使SCO连接已建立，下列限制应用在音频输出流以使他们被路由到SCO耳机：1.流类型必须是STREAM_VOICE_CALL 2.格式必须是mono 3.取样必须是16kH或8kHz。
下列限制应用在输出流： 1.格式必须是mono2.取样必须是16kH或8kHz。
注意电话应用程序总是有使用SCO连接的优先权。当电话在通话中，调用这个方法会被忽略。类似，当应用程序正在使用SCO连接，若接听来电或呼叫发送，连接会丢失且不会在通话结束后自动返回。
参照
stopBluetoothSco()
ACTION_SCO_AUDIO_STATE_CHANGED 
```  
public void stopBluetoothSco () 
```
停止蓝牙SCO音频连接。
需要权限：MODIFY_AUDIO_SETTINGS。
应用程序用startBluetoothSco()请求使用蓝牙SCO音频，当应用程序完成SCO连接或建立时间超时， 必须调用这个方法。
参照
startBluetoothSco()
```  
public void unloadSoundEffects () 
```
不加载音效。当音效被禁用时，调用这个方法释放一些内存。
```  
public void unregisterMediaButtonEventReceiver (ComponentName eventReceiver) 
```
移除注册MEDIA_BUTTON intent的接收器。
参数
eventReceiver 用registerMediaButtonEventReceiver(ComponentName)注册的BroadcastReceiver的标识符。