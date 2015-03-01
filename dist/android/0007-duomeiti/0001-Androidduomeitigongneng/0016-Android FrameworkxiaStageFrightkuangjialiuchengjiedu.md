#### 1、StageFright介绍
Android froyo版本多媒体引擎做了变动，新添加了stagefright框架，并且默认情况android选择stagefright，并没有完全抛弃opencore，主要是做了一个OMX层，仅仅是对opencore的omx-component部分做了引用。stagefright是在MediaPlayerService这一层加入的，和opencore是并列的。Stagefright在 Android中是以shared library的形式存在(libstagefright.so)，其中的module -- AwesomePlayer可用来播放video/audio。 AwesomePlayer提供许多API，可以让上层的应用程序(Java/JNI)来调用。
#### 2、StageFright数据流封装
2.1由数据源DataSource生成MediaExtractor。通过MediaExtractor::Create(dataSource)来实现。Create方法通过两步来生成相应的MediaExtractor(MediaExtractor.cpp)：
通过dataSource->sniff来探测数据类型
生成相应的Extractor：
```  
if (!strcasecmp(mime, MEDIA_MIMETYPE_CONTAINER_MPEG4)
		|| !strcasecmp(mime, "audio/mp4")) {
	return new MPEG4Extractor(source);
} else if (!strcasecmp(mime, MEDIA_MIMETYPE_AUDIO_MPEG)) {
	return new MP3Extractor(source, meta);
} else if (!strcasecmp(mime, MEDIA_MIMETYPE_AUDIO_AMR_NB)
		|| !strcasecmp(mime, MEDIA_MIMETYPE_AUDIO_AMR_WB)) {
	return new AMRExtractor(source);
} else if (!strcasecmp(mime, MEDIA_MIMETYPE_CONTAINER_WAV)) {
	return new WAVExtractor(source);
} else if (!strcasecmp(mime, MEDIA_MIMETYPE_CONTAINER_OGG)) {
	return new OggExtractor(source);
} else if (!strcasecmp(mime, MEDIA_MIMETYPE_CONTAINER_MATROSKA)) {
	return new MatroskaExtractor(source);
} else if (!strcasecmp(mime, MEDIA_MIMETYPE_CONTAINER_MPEG2TS)) {
	return new MPEG2TSExtractor(source);
}
```
2.2 把音视频轨道分离，生成mVideoTrack和mAudioTrack两个MediaSource。代码如下（AwesomePlayer.cpp）：
```  
if (!haveVideo && !strncasecmp(mime, "video/", 6)) {
	setVideoSource(extractor->getTrack(i));
	haveVideo = true;
} else if (!haveAudio && !strncasecmp(mime, "audio/", 6)) {
	setAudioSource(extractor->getTrack(i));
	haveAudio = true;
}
```
2.3 得到的这两个MediaSource，只具有parser功能，没有decode功能。还需要对这两个MediaSource做进一步的包装，获取了两个MediaSource（具有parse和decode功能）：
```  
mVideoSource = OMXCodec::Create(
	mClient.interface(), mVideoTrack->getFormat(),
	false, // createEncoder
	mVideoTrack,
	NULL, flags);
mAudioSource = OMXCodec::Create(
	mClient.interface(), mAudioTrack->getFormat(),
	false, // createEncoder
	mAudioTrack);
```
当调用MediaSource.start()方法后，它的内部就会开始从数据源获取数据并解析，等到缓冲区满后便停止。在AwesomePlayer里就可以调用MediaSource的read方法读取解码后的数据。
对于mVideoSource来说，读取的数据：mVideoSource->read(&mVideoBuffer, &options)交给显示模块进行渲染，mVideoRenderer->render(mVideoBuffer);
对mAudioSource来说，用mAudioPlayer对mAudioSource进行封装，然后由mAudioPlayer负责读取数据和播放控制。
#### 3、StageFright的Decode
经过“数据流的封装”得到的两个MediaSource，其实是两个OMXCodec。AwesomePlayer和mAudioPlayer都是从MediaSource中得到数据进行播放。AwesomePlayer得到的是最终需要渲染的原始视频数据，而mAudioPlayer读取的是最终需要播放的原始音频数据。也就是说，从OMXCodec中读到的数据已经是原始数据了。
OMXCodec是怎么把数据源经过parse、decode两步以后转化成原始数据的。从OMXCodec::Create这个构造方法开始，它的参数：
IOMX &omx指的是一个OMXNodeInstance对象的实例。
MetaData ＆meta这个参数由MediaSource.getFormat获取得到。这个对象的主要成员就是一个KeyedVector<uint32_t, typed_data> mItems，里面存放了一些代表MediaSource格式信息的名值对。
bool createEncoder指明这个OMXCodec是编码还是解码。
MediaSource ＆source是一个MediaExtractor。
char *matchComponentName指定一种Codec用于生成这个OMXCodec。
先使用findMatchingCodecs寻找对应的Codec，找到以后为当前IOMX分配节点并注册事件监听器：omx->allocateNode(componentName, observer, &node)。最后，把IOMX封装进一个OMXCodec：
```  
sp<OMXCodec> codec = new OMXCodec(
	omx, node, quirks,
	createEncoder, mime, componentName,
	source);
```
这样就得到了OMXCodec。
AwesomePlayer中得到这个OMXCodec后，首先调用mVideoSource->start()进行初始化。 OMXCodec初始化主要是做两件事：
向OpenMAX发送开始命令。mOMX->sendCommand(mNode, OMX_CommandStateSet, OMX_StateIdle)
调用allocateBuffers()分配两个缓冲区，存放在Vector<BufferInfo> mPortBuffers[2]中，分别用于输入和输出。
AwesomePlayer开始播放后，通过mVideoSource->read(&mVideoBuffer, &options)读取数据。mVideoSource->read(&mVideoBuffer, &options)具体是调用OMXCodec.read来读取数据。而OMXCodec.read主要分两步来实现数据的读取：
通过调用drainInputBuffers()对mPortBuffers[kPortIndexInput]进行填充，这一步完成parse。由OpenMAX从数据源把demux后的数据读取到输入缓冲区，作为OpenMAX的输入。
通过fillOutputBuffers()对mPortBuffers[kPortIndexOutput]进行填充，这一步完成decode。由OpenMAX对输入缓冲区中的数据进行解码，然后把解码后可以显示的视频数据输出到输出缓冲区。
AwesomePlayer通过mVideoRenderer->render(mVideoBuffer)对经过parse和decode处理的数据进行渲染。一个mVideoRenderer其实就是一个包装了IOMXRenderer的AwesomeRemoteRenderer：
```  
mVideoRenderer = new AwesomeRemoteRenderer(
	mClient.interface()->createRenderer(
	mISurface, component,
	(OMX_COLOR_FORMATTYPE)format,
	decodedWidth, decodedHeight,
	mVideoWidth, mVideoHeight,
	rotationDegrees));
```
#### 4、StageFright处理流程
Audioplayer为AwesomePlayer的成员，audioplayer通过callback来驱动数据的获取，awesomeplayer则是通过videoevent来驱动。二者有个共性，就是数据的获取都抽象成mSource->Read()来完成，且read内部把parser和dec 绑在一起。Stagefright AV同步部分，audio完全是callback驱动数据流，video部分在onVideoEvent里会获取audio的时间戳，是传统的AV时间戳做同步。
4.1 AwesomePlayer的Video主要有以下几个成员：
```  
mVideoSource(解码视频)
mVideoTrack(从多媒体文件中读取视频数据)
mVideoRenderer(对解码好的视频进行格式转换，android使用的格式为RGB565)
mISurface(重绘图层)
mQueue(event事件队列)
```
4.2 stagefright运行时的Audio部分抽象流程如下：
设置mUri的路径
启动mQueue，创建一个线程来运行 threadEntry（命名为TimedEventQueue，这个线程就是event调度器）。
打开mUri所指定的文件的头部，则会根据类型选择不同的分离器（如MPEG4Extractor）。
使用MPEG4Extractor对MP4进行音视频轨道的分离，并返回MPEG4Source类型的视频轨道给mVideoTrack
根据mVideoTrack中的编码类型来选择解码器，avc的编码类型会选择AVCDecoder，并返回给mVideoSource，并设置mVideoSource中的mSource为mVideoTrack。
插入onVideoEvent到Queue中，开始解码播放。
通过mVideoSource对象来读取解析好的视频buffer。
如果解析好的buffer还没到AV时间戳同步的时刻，则推迟到下一轮操作。
mVideoRenderer为空，则进行初始化（如果不使用 OMX会将mVideoRenderer设置为AwesomeLocalRenderer）。
通过mVideoRenderer对象将解析好的视频buffer转换成RGB565格式，并发给display模块进行图像绘制。
将onVideoEvent重新插入event调度器来循环。
4.3 数据由源到最终解码后的流程如下：
```  
URI,FD
｜
	DataSource
	｜
	MediaExtractor
		|
		mVideoTrack   mAudioTrack//音视频数据流
		｜
		mVideoSource   mAudioSource//音视频解码器
			|
		  mVideoBuffermAudioPlayer
```
说明：
设置DataSource，数据源可以两种URI和FD。URI可以http://，rtsp://等。FD是一个本地文件描述符，能过FD，可以找到对应的文件。
由DataSource生成MediaExtractor。通过sp<MediaExtractor> extractor = MediaExtractor::Create(dataSource);来实现。 MediaExtractor::Create(dataSource)会根据不同的数据内容创建不同的数据读取对象。
通过调用setVideoSource由MediaExtractor分解生成音频数据流（mAudioTrack）和视频数据流（mVideoTrack）。
onPrepareAsyncEvent()如果DataSource是URL的话，根据地址获取数据，并开始缓冲，直到获取到mVideoTrack和mAudioTrack。mVideoTrack和mAudioTrack通过调用initVideoDecoder()和initAudioDecoder()来生成mVideoSource和mAudioSource这两个音视频解码器。然后调用postBufferingEvent_l()提交事件开启缓冲。
数据缓冲的执行函数是onBufferingUpdate()。缓冲区有足够的数据可以播放时，调用play_l()开始播放。play_l()中关键是调用了postVideoEvent_l()，提交了mVideoEvent。这个事件执行时会调用函数onVideoEvent()。这个函数通过调用mVideoSource->read(&mVideoBuffer,&options)进行视频解码。音频解码通过mAudioPlayer实现。
视频解码器解码后通过mVideoSource->read读取一帧帧的数据，放到mVideoBuffer中，最后通过mVideoRenderer->render(mVideoBuffer)把视频数据发送到显示模块。当需要暂停或停止时，调用cancelPlayerEvents来提交事件用来停止解码，还可以选择是否继续缓冲数据。
#### 5、代码标记Log
依据第4项StageFright描述的Vide视频播放流程，作Log标记跟踪视频DATA获取、CODEC过程。从AwesomePlayer.cpp中方法着手，步骤如下：
在修改的/mydroid/frameworks/base/media/libstagefrigh/下，用mm编译，并调试直到生成相应的.so文件。注：允许单模块编译时，需事先在/mydroid下允许. ./build/envsetup.sh文件。
在/mydroid/目录下make进行整体编译，生成system.img文件。说明：先单模块编译，后再整体编译的好处是，可以缩短调试编译的时间。
将system.img文件copy到/android-sdk-linux/platforms/android-8/下。注意：事先备份原有的system.img。
带sdcard启动模拟器，在/android-sdk-linux/tools/下运行./adb shell文件，再运行logcat
打开Gallery选择视频文件运行，并同步查看log。
反馈结果如下：
```  
I/ActivityManager(61): Starting: Intent { act=android.intent.action.VIEW dat=content://media/external/video/media/5 typ=video/mp4 cmp=com.cooliris.media/.MovieView } from pid 327
I/RenderView(327): OnPause RenderView com.cooliris.media.RenderView@4054a3b0
E/AwesomePlayer(34): beginning AwesomePlayer... by jay remarked...
E/AwesomePlayer(34): returning AwesomeEvent...by jay remarked...
E/AwesomePlayer(34): returning AwesomeEvent...by jay remarked...
E/AwesomePlayer(34): returning AwesomeEvent...by jay remarked...
E/AwesomePlayer(34): returning AwesomeEvent...by jay remarked...
E/AwesomePlayer(34): ending AwesomePlayer... by jay remarked...
E/AwesomePlayer(34): setting video source now... by jay remarked...
E/AwesomePlayer(34): setting Video Type... by jay remarked...
E/AwesomePlayer(34): returning AwesomeEvent...by jay remarked...
E/AwesomePlayer(34): beginning initVideoDecoder by jay remarked...
D/MediaPlayer(327): getMetadata
I/ActivityManager(61): Displayed com.cooliris.media/.MovieView: +1s761ms
E/AwesomePlayer(34): beginning AwesomeLocalRenderer init ...by jay remarked...
E/AwesomePlayer(34): returning open(libstagefrighthw.so) correctly by jay remarked...
E/MemoryHeapBase(34): error opening /dev/pmem_adsp: No such file or directory
I/SoftwareRenderer(34): Creating physical memory heap failed, reverting to regular heap.
E/AwesomePlayer(34): ending AwesomeLocalRenderer init close ...by jay remarked...
E/AwesomePlayer(34): returning AwesomeLocalRenderer...by jay remarked...
I/CacheService(327): Starting CacheService
```