给大家详细介绍一下Android多媒体录制功能的一些实现方法。其中就包括对录音的实现方法。我们可以通过这篇文章介绍的内容详细了解Android录音失真的具体解决方法，以帮助大家解决实际应用中出现的问题。
在6410+WM9714的Android平台上测试MIC IN录音功能，出现一个BUG。在该平台声音播放是完全正常的，但是录音后再播放刚录的声音，会有失真，同样的录音文件在电脑上播放也一样，说明是Android录音失真的问题。后来通过打印9714的寄存器，发现录音频率是8000HZ，放音频率是44100HZ，这时基本上可以确定是由这个不匹配引起的。
我在Android代码里：AudioHardwareALSA.cpp文件中的函数中设定采样率，如下：
```  
AudioStreamInALSA::AudioStreamInALSA
(AudioHardwareALSA *parent) : mParent(parent)
{
	static StreamDefaults _defaults = {
		sampleRate : AudioRecord :: DEFAULT_SAMPLE_RATE,
	};
}
8.static const int DEFAULT_SAMPLE_RATE = 44100；
```
但是重烧程序后结果仍然存在Android录音失真这一问题，采样率还是8000，似乎被其他地方把值覆盖了。后来我试着把所有的采样率8000的地方全都改成44100，结果仍然一样是8000。到底是不是采样率的原因引起的呢？
之后用arecord命令来录音，前提是不能进入Android，否则音频设备会被占用。结果录得的声音播放时仍然是同样的效果，当时估计这问 题应该跟Android上层没有什么大的关系。后来别人有试通过将播放速度加快一倍，就得到的正常的播放音，但问题是仍然不知从何处来解决这个问题。
后来经过台湾同事的挖掘，改动录音MIC IN的DMA SIZE解决了此次问题。改动列出如下，原因尚待分析。
```  
static struct s3c24xx_pcm_dma_params
s3c6400_ac97_mic_mono_in = {
	client = &s3c6400_dma_client_micin,
	channel = DMACH_AC97_MIC_IN,
	dma_addr = S3C6400_PA_AC97 + S3C_AC97_MIC_DATA,
	dma_size = 2,///4
};
```
该参数最终在s3c24xx_pcm_hw_params中修改DMA配置所用。Android录音失真的相关解决办法就为大家介绍到这里。