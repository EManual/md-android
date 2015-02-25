Android中对于图形界面以及多媒体的相关操作都是比较容易实现的。而且对于很多数手机用户来说，他们主要也就是根据这些方面的功能来对系统那个进行修改。我们可以通过本文介绍的Android多媒体框架的源码解读，来具体分析一下这方面的基本知识。
Android多媒体框架的代码在以下目录中：external/opencore/。这个目录是Android多媒体框架的根目录，其中包含的子目录如下所示：
```  
* android：这里面是一个上层的库，它基于PVPlayer和PVAuthor的SDK实现了一个为Android使用的Player和Author。
* baselibs：包含数据结构和线程安全等内容的底层库。
* codecs_v2：这是一个内容较多的库，主要包含编解码的实现，以及一个OpenMAX的实现。
* engines：包含PVPlayer和PVAuthor引擎的实现。
* extern_libs_v2：包含了khronos的OpenMAX的头文件。
* fileformats：文件格式的据具体解析（parser）类。
* nodes：编解码和文件解析的各个node类。
* oscl：操作系统兼容库。
* pvmi：输入输出控制的抽象接口。
* protocols：主要是与网络相关的RTSP、RTP、HTTP等协议的相关内容。
* pvcommon：pvcommon库文件的Android.mk文件，没有源文件。
* pvplayer：pvplayer库文件的Android.mk文件，没有源文件。
* pvauthor：pvauthor库文件的Android.mk文件，没有源文件。
* tools_v2：编译工具以及一些可注册的模块。
```
#### Splitter的定义与初始化 
以wav的splitter为例，在fileformats目录下有解析wav文件格式的pvwavfileparser.cpp文件，在nodes目录下有pvmf_wavffparser_factory.cpp，pvmf_wavffparser_node.h， pvmf_wavffparser_port.h等文件。
我们由底往上看，vwavfileparser.cpp中的PV_Wav_Parser类有InitWavParser()，GetPCMData()，RetrieveFileInfo()等解析wav格式的成员函数，此类应该就是最终的解析类。我们搜索PV_Wav_Parser类被用到的地方可知，在PVMFWAVFFParserNode类中有PV_Wav_Parser的一个指针成员变量。
再搜索可知，PVMFWAVFFParserNode类是通过PVMFWAVFFParserNodeFactory的CreatePVMFWAVFFParserNode()成员函数生成的。而CreatePVMFWAVFFParserNode()函数是在PVPlayerNodeRegistry::PVPlayerNodeRegistry()类构造函数中通过PVPlayerNodeInfo类被注册到Oscl_Vector<PVPlayerNodeInfo,OsclMemAllocator>的vector中，在这个构造函数中，AMR，mp3等node也是同样被注册的。
由上可知，Android多媒体框架中对splitter的管理也是与ffmpeg等类似，都是在框架的初始化时注册的，只不过Opencore注册的是每个splitter的factory函数。
综述一下splitter的定义与初始化过程：
每个splitter都在fileformats目录下有个对应的子目录，其下有各自的解析类。
每个splitter都在nodes目录下有关对应的子目录，其下有各自的统一接口的node类和node factory类。
播放引擎PVPlayerEngine类中有PVPlayerNodeRegistry iPlayerNodeRegistry成员变量。
在PVPlayerNodeRegistry的构造函数中，将 AMR, AAC, MP3等splitter的输入与输出类型标示和node factory类中的create node与release delete接口通过PVPlayerNodeInfo类push到Oscl_Vector<PVPlayerNodeInfo, OsclMemAllocator> iType成员变量中。
#### 当前Splitter的匹配过程 
PVMFStatus PVPlayerNodeRegistry::QueryRegistry(PVMFFormatType&aInputType,PVMFFormatType&aOutputType,Oscl_Vector<PVUuid,OsclMemAllocator>&aUuids)函数的功能是根据输入类型和输出类型，在已注册的node vector中寻找是否有匹配的node，有的话传回其唯一识别标识PVUuid。
从QueryRegistry这个函数至底向上搜索可得到，在android中splitter的匹配过程如下：
android_media_MediaPlayer.cpp之中定义了一个JNINativeMethod（JAVA本地调用方法）类型的数组gMethods，供java代码中调用MultiPlayer类的setDataSource成员函数时找到对应的c++函数
```  
{
	"setDataSource", 
	"(Ljava/lang/String;)V", 
	(void *) android_media_MediaPlayer_setDataSource},
	static void android_media_MediaPlayer_setDataSource (JNIEnv *env, jobject thiz, jstring path) 
}
```
此函数中先得到当前的MediaPlayer实例，然后调用其setDataSource函数，传入路径
```  
status_t MediaPlayer::setDataSource(const char *url) 
```
此函数通过调getMediaPlayerService()先得到当前的MediaPlayerService，const sp<IMediaPlayerService>&service(getMediaPlayerService()); 
然后新建一个IMediaPlayer变量，sp<IMediaPlayer> player(service->create(getpid(), this, fd, offset, length));
在sp<IMediaPlayer> MediaPlayerService::create(pid_t pid, const sp<IMediaPlayerClient>& client, const char* url)中调status_t MediaPlayerService::Client::setDataSource(const char *url)函数，Client是MediaPlayerService的一个内部类。
在MediaPlayerService::Client::setDataSource中，调 sp<MediaPlayerBase> MediaPlayerService::Client::createPlayer(player_type playerType)
生成一个继承自MediaPlayerBase的PVPlayer实例。