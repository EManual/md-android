#### media库中的Audio框架部分
Android的Audio系统的核心框架在media库中提供，对上面主要实现AudioSystem、AudioTrack和AudioRecorder三个类。
提供了IAudioFlinger类接口，在这个类中，可以获得IAudioTrack和IAudioRecorder两个接口，分别用于声音的播放和录制。AudioTrack和AudioRecorder分别通过调用IAudioTrack和IAudioRecorder来实现。
Audio系统的头文件在frameworks/base/include/media/目录中，主要的头文件如下：
AudioSystem.h：media库的Audio部分对上层的总管接口；
IAudioFlinger.h：需要下层实现的总管接口；
AudioTrack.h：放音部分对上接口；
IAudioTrack.h：放音部分需要下层实现的接口；
AudioRecorder.h：录音部分对上接口；
IAudioRecorder.h：录音部分需要下层实现的接口。
IAudioFlinger.h、IAudioTrack.h和IAudioRecorder.h这三个接口通过下层的继承来实现（即AudioFlinger）。AudioFlinger.h、AudioTrack.h和AudioRecorder.h是对上层提供的接口，它们既供本地程序调用（例如声音的播放器、录制器等），也可以通过JNI向Java层提供接口。
meida库中Audio部分的结构如图2所示。

![img](http://emanual.github.io/md-android/img/device_audio/04_audio.png) 

图2  meida库中Audio部分的结构
从功能上看，AudioSystem负责的是Audio系统的综合管理功能，而AudioTrack和AudioRecorder分别负责音频数据的输出和输入，即播放和录制。
AudioSystem.h中主要定义了一些枚举值和set/get等一系列接口，如下所示：
```  
class AudioSystem  
{  
public:  
enum stream_type {        // Audio 流的类型
        SYSTEM          = 1,
        RING            = 2,
        MUSIC           = 3,
        ALARM           = 4,
        NOTIFICATION    = 5,
        BLUETOOTH_SCO   = 6,
        ENFORCED_AUDIBLE = 7,
        NUM_STREAM_TYPES
    };  
enum audio_output_type {            // Audio数据输出类型
// …… 省略部分内容 
}; 
enum audio_format {                 // Audio数据格式
        FORMAT_DEFAULT = 0,
        PCM_16_BIT,
        PCM_8_BIT,
        INVALID_FORMAT
    };  
enum audio_mode {                       // Audio模式
// …… 省略部分内容 
}; 
enum audio_routes {                 // Audio 路径类型
        ROUTE_EARPIECE         = (1 << 0),
        ROUTE_SPEAKER          = (1 << 1),
        ROUTE_BLUETOOTH_SCO  = (1 << 2),
        ROUTE_HEADSET           = (1 << 3),
        ROUTE_BLUETOOTH_A2DP  = (1 << 4),
        ROUTE_ALL                 = -1UL,
    };  
// …… 省略部分内容
static status_t setMasterVolume(float value);  
static status_t setMasterMute(bool mute);  
static status_t getMasterVolume(float* volume);  
static status_t getMasterMute(bool* mute);  
static status_t setStreamVolume(int stream, float value);  
static status_t setStreamMute(int stream, bool mute);  
static status_t getStreamVolume(int stream, float* volume);  
static status_t getStreamMute(int stream, bool* mute);  
static status_t setMode(int mode);  
static status_t getMode(int* mode);  
static status_t setRouting(int mode,
uint32_t routes, uint32_t mask);  
static status_t getRouting(int mode, uint32_t* routes);  
// …… 省略部分内容
};
```
在Audio系统的几个枚举值中，audio_routes是由单独的位来表示的，而不是由顺序的枚举值表示，因此这个值在使用过程中可以使用"或"的方式。例如，表示声音可以既从耳机（EARPIECE）输出，也从扬声器（SPEAKER）输出，这样是否能实现，由下层提供支持。在这个类中，set/get等接口控制的也是相关的内容，例如Audio声音的大小、Audio的模式、路径等。
AudioTrack是Audio输出环节的类，其中最重要的接口是write()，主要的函数如下所示。
```  
class AudioTrack  
{  
    typedef void (*callback_t)(int event,  void* user, void *info);
    AudioTrack( int streamType,
         uint32_t sampleRate  = 0,  // 音频的采样律
         int format           = 0,  //音频的格式（例如8位或者16位的PCM）
         int channelCount     = 0,  // 音频的通道数
         int frameCount       = 0,  // 音频的帧数
         uint32_t flags = 0,  callback_t cbf = 0,  void* user = 0,  int notificationFrames = 0);
    void start();  
    void stop();  
    void flush();  
    void pause();  
    void mute(bool);  
    ssize_t write(const
    void* buffer, size_t size);
    // …… 省略部分内容
}
```
AudioRecord是Audio输入环节的类，其中最重要的接口为read()，主要的函数如下所示。.
```  
class AudioRecord  
{  
	public:
    AudioRecord(int streamType,  uint32_t sampleRate  = 0,       // 音频的采样律
        int format = 0,       // 音频的格式（例如8位或者16位的PCM）
        int channelCount = 0,       // 音频的通道数
        int frameCount = 0,       // 音频的帧数
        uint32_t flags = 0,
        callback_t cbf = 0,
        void* user = 0,
        int notificationFrames = 0);  
	    status_t  start();
	    status_t   stop();
	    ssize_t    read(void* buffer, size_t size);
        // …… 省略部分内容
}
```
AudioTrack和AudioRecord的read/write函数的参数都是内存的指针及其大小，内存中的内容一般表示的是Audio的原始数据（PCM数据）。这两个类还涉及Auido数据格式、通道数、帧数目等参数，可以在建立时指定，也可以在建立之后使用set()函数进行设置。
在libmedia库中提供的只是一个Audio系统框架，AudioSystem、AudioTrack和AudioRecord分别调用下层的IAudioFlinger、IAudioTrack和IAudioRecord来实现。另外的一个接口是IAudioFlingerClient，它作为向IAudioFlinger中注册的监听器，相当于使用回调函数获取IAudioFlinger运行时信息。