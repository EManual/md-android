#### 用桩实现的Audio硬件抽象层
AudioHardwareStub.h和AudioHardwareStub.cpp是一个Android硬件抽象层的桩实现方式。这个实现不操作实际的硬件和文件，它所进行的是空操作，在系统没有实际的Audio设备时使用这个实现，来保证系统的正常工作。如果使用这个硬件抽象层，实际上Audio系统的输入和输出都将为空。
AudioHardwareStub.h定义了AudioStreamOutStub 和AudioStreamInStub类的情况
如下所示：
```  
class AudioStreamOutStub : public AudioStreamOut {  
public:  
    virtual status_t set(int format, int channelCount, uint32_t sampleRate);
    virtual uint32_t sampleRate() const { return 44100; }
    virtual size_t bufferSize() const { return 4096; }
    virtual int channelCount() const { return 2; }
    virtual int format() const { return AudioSystem::PCM_16_BIT; }
    virtual uint32_t latency() const { return 0; }
    virtual status_t setVolume(float volume) { return NO_ERROR; }
    virtual ssize_t write(const void* buffer, size_t bytes);
    virtual status_t standby();
    virtual status_t dump(int fd, const Vector<String16>& args);
};  
class AudioStreamInStub : public AudioStreamIn {  
public:  
    virtual status_t set(int format, int channelCount, uint32_t sampleRate, AudioSystem::audio_in_acoustics acoustics);
    virtual uint32_t sampleRate() const { return 8000; }
    virtual size_t bufferSize() const { return 320; }
    virtual int channelCount() const { return 1; }
    virtual int format() const { return AudioSystem::PCM_16_BIT; }
    virtual status_t setGain(float gain) { return NO_ERROR; }
    virtual ssize_t read(void* buffer, ssize_t bytes);
    virtual status_t dump(int fd, const Vector<String16>& args);
    virtual status_t standby() { return NO_ERROR; }
};
```
上面实际上使用了最简单模式，只是用固定的参数（缓冲区大小、采样率、通道数），以及将一些函数直接无错误返回。
使用AudioHardwareStub类来继承AudioHardwareBase，事实上也就是继承AudioHardwareInterface。
```  
class AudioHardwareStub : public  AudioHardwareBase  
{  
public:  
    AudioHardwareStub();
    virtual ~AudioHardwareStub();
    virtual status_t initCheck();
    virtual status_t setVoiceVolume(float volume);
    virtual status_t setMasterVolume(float volume);
    virtual status_t setMicMute(bool state){ mMicMute = state;  return  NO_ERROR; }
    virtual status_t getMicMute(bool* state){ *state = mMicMute ; return NO_ERROR; }
    virtual status_t setParameter(const char* key, const char* value)   { return NO_ERROR; }
    virtual AudioStreamOut* openOutputStream( //打开输出流
          int format=0,  
          int channelCount=0,  
          uint32_t sampleRate=0,
          status_t *status=0);
   virtual AudioStreamIn* openInputStream( //打开输入流
          int format,  
          int channelCount,  
          uint32_t sampleRate,
          status_t *status,
          AudioSystem::audio_in_acoustics acoustics);
   // …… 省略部分内容
};
```
#### AudioFlinger中自带Audio硬件抽象层实现（2）
在实现过程中，为了保证声音可以输入和输出，这个桩实现的主要内容是实现AudioStreamOutStub和AudioStreamInStub类的读/写函数。实现如下所示：
ssize_t AudioStreamOutStub::write(const
void* buffer, size_t bytes)  
{  
    usleep(bytes * 1000000 / sizeof(int16_t) /channelCount() / sampleRate());
    return bytes;  
}  
ssize_t AudioStreamInStub::read(void* buffer, ssize_t bytes)  
{  
    usleep(bytes * 1000000 / sizeof(int16_t) / channelCount() / sampleRate());
    memset(buffer, 0, bytes);
    return bytes;  
}
```
由此可见，使用这个接口进行音频的输入和输出时，和真实的设备没有关系，输出和输入都使用延时来完成。对于输出的情况，不会有声音播出，但是返回值表示全部内容已经输出完成；对于输入的情况，将返回全部为0的数据。
此外，这种实现支持默认的参数，如果用set()函数设置的参数与默认参数不一致，还会返回错误。
#### Android通用的Audio硬件抽象层
AudioHardwareGeneric.h和AudioHardwareGeneric.cpp是Android通用的一个Audio硬件抽象层。与前面的桩实现不同，这是一个真正能够使用的Audio硬件抽象层，但是它需要Android的一种特殊的声音驱动程序的支持。
与前面类似，AudioStreamOutGeneric、AudioStreamInGeneric和AudioHardwareGeneric这3个类分别继承Audio硬件抽象层的3个接口。
```  
class AudioStreamOutGeneric : public AudioStreamOut {
	// ...... 通用Audio输出类的接口.
};  
class AudioStreamInGeneric : public AudioStreamIn { 
	// ...... 通用Audio输入类的接口
};  
class AudioHardwareGeneric : public AudioHardwareBase  
{  
	// ...... 通用Audio控制类的接口
};
```
在AudioHardwareGeneric.cpp的实现中，使用的驱动程序是/dev/eac，这是一个非标准程序，定义设备的路径如下所示：
```  
static char const * const kAudioDeviceName = "/dev/eac";
```
对于Linux操作系统，这个驱动程序在文件系统中的节点主设备号为10，次设备号自动生成。
提示：eac是Linux中的一个misc驱动程序，作为Android的通用音频驱动，写设备表示放音，读设备表示录音。
在AudioHardwareGeneric的构造函数中，打开这个驱动程序的设备节点。
```  
AudioHardwareGeneric::AudioHardwareGeneric() : mOutput(0), mInput(0),  mFd(-1), mMicMute(false)  
{  
	mFd = :: open(kAudioDeviceName, O_RDWR); //打开通用音频设备的节点
}
```
这个音频设备是一个比较简单的驱动程序，没有很多设置接口，只是用写设备表示录音，读设备表示放音。放音和录音支持的都是16位的PCM。
```  
ssize_t AudioStreamOutGeneric::write(const
void* buffer, size_t bytes)  
{  
    Mutex::Autolock _l(mLock);
    return ssize_t(::write(mFd, buffer, bytes)); //写入硬件设备
}  
ssize_t AudioStreamInGeneric::read(void* buffer, ssize_t bytes)  
{  
    AutoMutex lock(mLock);
    if (mFd < 0) {  
       return NO_INIT;  
    }  
    return ::read(mFd, buffer, bytes); // 读取硬件设备
}
```
虽然AudioHardwareGeneric是一个可以真正工作的Audio硬件抽象层，但是这种实现方式非常简单，不支持各种设置，参数也只能使用默认的。而且，这种驱动程序需要在Linux核心加入eac驱动程序的支持。