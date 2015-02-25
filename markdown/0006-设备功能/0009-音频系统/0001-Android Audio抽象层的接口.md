#### Audio硬件抽象层的接口定义
Audio的硬件抽象层是AudioFlinger和Audio硬件的接口，在各个系统的移植过程中可以有不同的实现方式。Audio硬件抽象层的接口路径为：
```  
hardware/libhardware_legacy/include/hardware/
```
其中主要的文件为：AudioHardwareBase.h和AudioHardwareInterface.h。
Android中的Audio硬件抽象层可以基于Linux标准的ALSA或OSS音频驱动实现，也可以基于私有的Audio驱动接口来实现。
在AudioHardwareInterface.h中定义了类：AudioStreamOut、AudioStreamIn和AudioHardwareInterface。
AudioStreamOut和AudioStreamIn的主要定义如下所示：
```  
class AudioStreamOut { 
	public: virtual ~AudioStreamOut() = 0;
	virtual status_t setVolume(float volume) = 0;
	virtual ssize_t write(const void* buffer, size_t bytes) = 0;
	//...... 省略部分内容 
}; 
class AudioStreamIn { 
	public: virtual ~AudioStreamIn() = 0;
	virtual status_t setGain(float gain) = 0;
	virtual ssize_t read(void* buffer, ssize_t bytes) = 0;
	//...... 省略部分内容 
};
```
AudioStreamOut和AudioStreamIn分别对应了音频的输出环节和输入环节，其中负责数据流的接口分别是wirte()和read()，参数是一块内存的指针和长度；另外还有一些设置和获取接口。
Audio的硬件抽象层主体AudioHardwareInterface类的定义如下所示：
```  
class AudioHardwareInterface { 
	public:
	virtual status_t initCheck() = 0;
	virtual status_t setVoiceVolume(float volume) = 0;
	virtual status_t setMasterVolume(float volume) = 0;
	virtual status_t setRouting(int mode, uint32_t routes) = 0;
	virtual status_t getRouting(int mode, uint32_t* routes) = 0;
	virtual status_t setMode(int mode) = 0;
	virtual status_t getMode(int* mode) = 0;
	//...... 省略部分内容 
	virtual AudioStreamOut* openOutputStream(
	// 打开输出流 
	int format=0, int channelCount=0, uint32_t sampleRate=0, status_t *status=0) = 0; 
	virtual AudioStreamIn* openInputStream(
	// 打开输入流 
	int format, int channelCount, uint32_t sampleRate, status_t *status, AudioSystem::audio_in_acoustics acoustics) = 0; 
	static AudioHardwareInterface* create(); 
};
```
在这个AudioHardwareInterface接口中，使用openOutputStream()和openInputStream()函数分别获取AudioStreamOut和AudioStreamIn两个类，它们作为音频输入/输出设备来使用。
此外，AudioHardwareInterface.h定义了C语言的接口来获取一个AudioHardware Interface类型的指针。
```  
extern "C" AudioHardwareInterface* createAudioHardware(void);
```
如果实现一个Android的硬件抽象层，则需要实现AudioHardwareInterface、AudioStreamOut和AudioStreamIn三个类，将代码编译成动态库libauido.so。AudioFlinger会连接这个动态库，并调用其中的createAudioHardware()函数来获取接口。
在AudioHardwareBase.h中定义了类：AudioHardwareBase，它继承了Audio HardwareInterface，显然继承这个接口也可以实现Audio的硬件抽象层。
提示：Android系统的Audio硬件抽象层可以通过继承类AudioHardwareInterface来实现，其中分为控制部分和输入/输出处理部分。