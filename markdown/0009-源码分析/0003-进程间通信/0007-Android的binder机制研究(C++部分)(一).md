#### 概述    
android的binder机制提供一种进程间通信的方法，使一个进程可以以类似远程过程调用的形式调用另一个进程所提供的功能。binder机制在Java环境和C/C++环境都有提供。
android的代码中，与C/C++的binder包括一些类型和接口的定义和实现，相关的代码在下面这几个文件中：
```  
frameworks\base\include\utils\IInterface.h
frameworks\base\include\utils\Binder.h
frameworks\base\include\utils\BpBinder.h
frameworks\base\include\utils\IBinder
frameworks\base\include\utils\Parcel.h
frameworks\base\include\utils\IPCThreadState.h
frameworks\base\include\utils\ProcessState.h
frameworks\base\libs\utils\Binder.cpp
frameworks\base\libs\utils\BpBinder.cpp
frameworks\base\libs\utils\IInterface.cpp
frameworks\base\libs\utils\IPCThreadState.cpp
frameworks\base\libs\utils\Parcel.cpp
frameworks\base\libs\utils\ProcessState.cpp	
```
为了了解这些类、接口之间的关系以及binder的实现机制，最好是结合一个例子来进行研究。我选择的例子是android自带的媒体播放器的实现。其媒体播放器的相关代码在下面这些目录中：
frameworks\base\include\media
frameworks\base\media
使用startUML的反向工程功能分析上面这些代码，并进行了一定的整理之后，得到下面这幅类图
![img](P)  
Android的媒体播放功能分成两部分，一部分是媒体播放应用，一部分是媒体播放服务(MediaServer，在系统启动时由init所启动，具可参考init.rc文件)。这两部分分别跑在不同的进程中。媒体播放应用包括Java程序和部分C++代码，媒体播放服务是C++代码，并且需要调用外部模块opencore来实现真正的媒体播放。媒体播放应用和媒体播放服务之间需要通过binder机制来进行相互调用，这些调用包括：
(1)媒体播放应用向媒体播放服务发控制指令
(2)媒体播放服务向媒体播放应用发事件通知(notify)
媒体播放服务对外提供多个接口，在上面得图中包括其中的2个接口：IMediaService和IMediaPlayer，IMediaplayer用于创建和管理播放实例，而IMediaplayer接口则是播放接口，用于实现指定媒体文件的播放以及播放过程的控制。
上面的图中还有媒体播放应用向媒体播放服务提供的1个接口：IMediaPlayerClient，用于接收notify。
这些接口因为需要跨进程调用，因此需要用到binder机制。每个接口包括两部分实现，一部分是接口功能的真正实现(BnInterface)，这部分运行在接口提供进程中；另一部分是接口的proxy(BpInterface)，这部分运行在调用接口的进程中。binder的作用就是让这两部分之间建立联系。下图是整个播放器的一个概要说明。
![img](P)  
媒体播放器比较复杂一些，总共实现了3个接口，不过要了解binder的机制，只需要研究其中一个接口就足够了。在这里选择IMediaPlayerService接口来看一下。
IMediaPlayerService接口包括六个功能函数：create(url)、create(fd)、decode(url)、decode(fd)、createMediaRecord()、createMetadataRetriever()。在这里不介绍这些函数是做什么的，我们只关注如何通过binder还提供这些函数接口。
#### (二) 接口定义
#### (1) 定义接口类
首先定义IMediaPlayerService类，这是一个接口类(C++的术语应该叫纯虚类)。该接口类定义在文件frameworks\base\include\media\IMediaPlayerService.h。代码如下：
```  
class IMediaPlayerService: public IInterface
{
public:
DECLARE_META_INTERFACE(MediaPlayerService);
virtual sp<IMediaRecorder> createMediaRecorder(pid_t pid) = 0;
virtual sp<IMediaMetadataRetriever> createMetadataRetriever(pid_t pid) = 0;
virtual sp<IMediaPlayer> create(pid_t pid, const sp<IMediaPlayerClient>& client, const char* url) = 0;
virtual sp<IMediaPlayer> create(pid_t pid, const sp<IMediaPlayerClient>& client, int fd, int64_t offset, int64_t length) = 0;
virtual sp<IMemory> decode(const char* url, uint32_t *pSampleRate, int* pNumChannels, int* pFormat) = 0;
virtual sp<IMemory> decode(int fd, int64_t offset, int64_t length, uint32_t *pSampleRate, int* pNumChannels, int* pFormat) = 0;
};	
```
可以看到，在这个接口类中定义了IMediaPlayerService需要提供的6个函数接口，因为是接口类，所以定义为纯虚函数。需要注意这个接口类的名称有严格要求，必须是以大写字母I开始。
重点关注在这些函数前面的一个宏定义： DECLARE_META_INTERFACE(MediaPlayerService)。这个宏定义必须要有，其中封装了实现binder所需要的一些类成员变量和成员函数通过这些成员函数可以为一个binder实现创建proxy。这个宏定义在问价frameworks\base\include\utils\IInterface.h里，在后面还会讲到。这个宏定义的参数必须是接口类的名称去除字母I后剩下的部分。
另外说明一下，可以看到接口类中所定义的函数的返回值都是sp<xxxx>的形式，看起来有点怪异。sp是android中定义的一个模板类，用于实现智能指针功能。sp<IMediaPlayer>就是IMediaPlayer的智能指针，可以简单地把它看成是标准C++中的指针定义即 IMediaPlayer* 即可。