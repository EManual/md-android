#### 提供Dump功能的Audio硬件抽象层
AudioDumpInterface.h和AudioDumpInterface.cpp是一个提供了Dump功能的Audio硬件抽象层，它所起到的作用就是将输出的Audio数据写入到文件中。
AudioDumpInterface本身支持Audio的输出功能，不支持输入功能。AudioDumpInterface.h中的类定义如下：
```  
class AudioStreamOutDump : public AudioStreamOut {  
public:  
	AudioStreamOutDump( AudioStreamOut* FinalStream);
	~AudioStreamOutDump();
	virtual ssize_t write(const void* buffer, size_t bytes);
	virtual uint32_t sampleRate() const { return mFinalStream->sampleRate(); }
	virtual size_t bufferSize() const { return mFinalStream->bufferSize(); }
	virtual int channelCount() const { return mFinalStream->channelCount(); }
	virtual int format() const { return mFinalStream->format(); }
	virtual uint32_t latency() const { return mFinalStream->latency(); }
	virtual status_t setVolume(float volume)  { return  mFinalStream->setVolume(volume); }
	virtual status_t standby();
	// …… 省略部分内容
};  
class AudioDumpInterface : public AudioHardwareBase  
{  
    virtual AudioStreamOut* openOutputStream(int format=0, int channelCount=0, uint32_t sampleRate=0, status_t *status=0);
	// …… 省略部分内容
}
```
只实现了AudioStreamOut，没有实现AudioStreamIn，因此这个Audio硬件抽象层只支持输出功能，不支持输入功能。
输出文件的名称被定义为：
1. define FLINGER_DUMP_NAME "/data/FlingerOut.pcm"
在AudioDumpInterface.cpp的AudioStreamOut所实现的写函数中，写入的对象就是这个文件。
```  
ssize_t AudioStreamOutDump::write(const
void* buffer, size_t bytes)  
{  
    ssize_t ret;
    ret = mFinalStream->write(buffer, bytes);
    if(!mOutFile && gFirst) {
        gFirst = false;
        mOutFile = fopen(FLINGER_DUMP_NAME, "r");
        if(mOutFile) {
            fclose(mOutFile);
            mOutFile = fopen(FLINGER_DUMP_NAME, "ab");// 鎵撳紑杈撳嚭鏂囦欢
        }  
    }  
    if (mOutFile) {  
        fwrite(buffer, bytes, 1, mOutFile);  // 鍐欐枃浠惰緭鍑哄唴瀹?
   }
   return ret;  
}
```
如果文件是打开的，则使用追加方式写入。因此使用这个Audio硬件抽象层时，播放的内容（PCM）将全部被写入文件。而且这个类支持各种格式的输出，这取决于调用者的设置。
AudioDumpInterface并不是为了实际的应用使用的，而是为了调试使用的类。当进行音频播放器调试时，有时无法确认是解码器的问题还是Audio输出单元的问题，这时就可以用这个类来替换实际的Audio硬件抽象层，将解码器输出的Audio的PCM数据写入文件中，由此可以判断解码器的输出是否正确。
提示：使用AudioDumpInterface音频硬件抽象层，可以通过/data/FlingerOut.pcm文件找到PCM的输出数据。
#### Audio硬件抽象层的真正实现
实现一个真正的Audio硬件抽象层，需要完成的工作和实现以上的硬件抽象层类似。
例如：可以基于Linux标准的音频驱动：OSS（Open Sound System）或者ALSA（Advanced Linux Sound Architecture）驱动程序来实现。
对于OSS驱动程序，实现方式和前面的AudioHardwareGeneric类似，数据流的读/写操作通过对/dev/dsp设备的读/写来完成；区别在于OSS支持了更多的ioctl来进行设置，还涉及通过/dev/mixer设备进行控制，并支持更多不同的参数。
对于ALSA驱动程序，实现方式一般不是直接调用驱动程序的设备节点，而是先实现用户空间的alsa-lib，然后Audio硬件抽象层通过调用alsa-lib来实现。
在实现Audio硬件抽象层时，对于系统中有多个Audio设备的情况，可由硬件抽象层自行处理setRouting()函数设定，例如，可以选择支持多个设备的同时输出，或者有优先级输出。对于这种情况，数据流一般来自AudioStreamOut::write()函数，可由硬件抽象层确定输出方法。对于某种特殊的情况，也有可能采用硬件直接连接的方式，此时数据流可能并不来自上面的write()，这样就没有数据通道，只有控制接口。Audio硬件抽象层也是可以处理这种情况的。