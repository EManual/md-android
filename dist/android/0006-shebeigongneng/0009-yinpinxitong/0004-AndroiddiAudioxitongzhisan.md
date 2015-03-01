#### AudioFlinger本地代码
AudioFlinger是Audio系统的中间层，在系统中起到服务作用，它主要作为libmedia提供的Audio部分接口的实现，其代码路径为：
1.frameworks/base/libs/audioflinger
AudioFlinger的核心文件是AudioFlinger.h和AudioFlinger.cpp，提供了类AudioFlinger，这个类是一个IAudioFlinger的实现，其主要接口如下所示：
```  
class AudioFlinger : public BnAudioFlinger, public IBinder::DeathRecipient  
{  
	public:
    // …… 省略部分内容
    virtual sp<IAudioTrack> createTrack(
	   // 获得音频输出接口（Track）
		pid_t pid,
		int streamType,  
		uint32_t sampleRate,
		int format, 
		int channelCount,  
		int frameCount,  
		uint32_t flags,
		const sp<IMemory>& sharedBuffer, status_t *status);
    // …… 省略部分内容
    virtual status_t setMasterVolume(float value);
    virtual status_t setMasterMute(bool muted);
    virtual status_t setStreamVolume(int stream, float value);
    virtual status_t setStreamMute(int stream, bool muted);

    virtual status_t setRouting(int mode, uint32_t routes, uint32_t mask);
    virtual uint32_t getRouting(int mode) const;
    virtual status_t setMode(int mode);
    virtual int getMode() const;
    virtual sp<IAudioRecord> openRecord(// 获得音频输出接口（Record）
		pid_t pid,
		int streamType,  
		uint32_t sampleRate,
		int format,  
		int channelCount,  
		int frameCount,  
		uint32_t flags,
		status_t *status);
}
```
AudioFlinger主要提供createTrack()创建音频的输出设备IAudioTrack，openRecord()创建音频的输入设备IAudioRecord。另外包含的就是一个get/set接口，用于控制。
AudioFlinger构造函数片段如下所示：
```  
AudioFlinger::AudioFlinger()  
{  
    mHardwareStatus = AUDIO_HW_IDLE;
    mAudioHardware = AudioHardwareInterface::create();
    mHardwareStatus = AUDIO_HW_INIT;
    if (mAudioHardware->initCheck() == NO_ERROR) {  
            mHardwareStatus = AUDIO_HW_OUTPUT_OPEN;
            status_t status;
            AudioStreamOut *hwOutput =
            mAudioHardware->openOutputStream(AudioSystem::PCM_16_BIT, 0, 0, &status);
            mHardwareStatus = AUDIO_HW_IDLE;

    if (hwOutput) {  
            mHardwareMixerThread = new MixerThread(this, hwOutput,AudioSystem::AUDIO_OUTPUT_HARDWARE);
    } else {  
            LOGE("Failed to initialize hardware output stream, status: %d", status);
    }  
    // …… 省略部分内容
    mAudioRecordThread = new AudioRecordThread(mAudioHardware, this);
    if (mAudioRecordThread != 0) {  
            mAudioRecordThread->run("AudioRecordThread", PRIORITY_URGENT_AUDIO);
    }else {  
            LOGE("Couldn't even initialize the stubbed audio hardware!");
    }  
}
```
从工作的角度看，AudioFlinger在初始化之后，首先获得放音设备，然后为混音器（Mixer）建立线程，接着建立放音设备线程，在线程中获得放音设备。
在AudioFlinger的AudioResampler.h中定义了一个音频重取样器工具类，如下所示：
```  
class AudioResampler {  
	public:
	enum src_quality {
        DEFAULT=0,
        LOW_QUALITY=1,  // 线性差值算法
        MED_QUALITY=2,  // 立方差值算法
        HIGH_QUALITY=3  // fixed multi-tap FIR算法
    };  
    static AudioResampler* create(int bitDepth, int inChannelCount, // 静态地创建函数
                    int32_t sampleRate, int quality=DEFAULT);
    virtual ~AudioResampler();
    virtual void init() = 0;
    virtual void setSampleRate(int32_t inSampleRate); // 设置重采样率
    virtual void setVolume(int16_t left, int16_t right); // 设置音量
    virtual void resample(int32_t* out, size_t outFrameCount,  AudioBufferProvider* provider) = 0;
};
```
这个音频重取样工具包含3种质量：低等质量（LOW_QUALITY）将使用线性差值算法实现；中等质量（MED_QUALITY）将使用立方差值算法实现；高等质量（HIGH_ QUALITY）将使用FIR（有限阶滤波器）实现。AudioResampler中的AudioResamplerOrder1是线性实现，AudioResamplerCubic.*文件提供立方实现方式，AudioResamplerSinc.*提供FIR实现。
AudioMixer.h和AudioMixer.cpp中实现的是一个Audio系统混音器，它被AudioFlinger调用，一般用于在声音输出之前的处理，提供多通道处理、声音缩放、重取样。AudioMixer调用了AudioResampler。
提示： AudioFlinger本身的实现通过调用下层的Audio硬件抽象层的接口来实现具体的功能，各个接口之间具有对应关系。