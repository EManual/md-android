整体框架图

![img](http://emanual.github.io/md-android/img/media_media/01_media.jpg) 

整个MediaPlayer在运行的时候，可以大致上分成Client和Server两个部分，它们分别在两个进程中运行，它们之间使用Binder机制实现IPC通讯。从框架结构上来看，IMediaPlayerService.h、IMediaPlayerClient.h和MediaPlayer.h三个类定义了MeidaPlayer的接口和架构，MediaPlayerService.cpp和mediaplayer.coo两个文件用于MeidaPlayer架构的实现，MeidaPlayer的具体功能在PVPlayer（库libopencoreplayer.so）中的实现。
IMediaPlayerClient.h用于描述一个MediaPlayer客户端的接口，描述如下所示：
```  
class IMediaPlayerClient: public IInterface
{
	public:
	DECLARE_META_INTERFACE(MediaPlayerClient);
	virtual void notify(int msg, int ext1, int ext2)= 0;

};
class BnMediaPlayerClient: publicBnInterface<IMediaPlayerClient>
{
	public:
	virtualstatus_t onTransact( uint32_t code,
	const Parcel& data,
	Parcel* reply,
	uint32_t flags = 0);
};
```
在定义中，IMediaPlayerClient类继承IInterface，并定义了一个MediaPlayer客户端的接口，BnMediaPlayerClient继承了BnInterface<IMediaPlayerClient>，这是为基于Android的基础类Binder机制实现在进程通讯而构建的。事实上，根据BnInterface类模版的定义BnInterface<IMediaPlayerClient>类相当于双继承了BnInterface和ImediaPlayerClient。这是Android一种常用的定义方式。
mediaplayer.h是对外的接口类，它最主要是定义了一个MediaPlayer类：
```  
class MediaPlayer : public BnMediaPlayerClient
{
	public:
	MediaPlayer();
	~MediaPlayer();
	void onFirstRef();
	void disconnect();
	status_t setDataSource(const char*url);
	status_t setDataSource(int fd,int64_t offset, int64_t length);
	status_t setVideoSurface(constsp<Surface>& surface);
	status_t setListener(constsp<MediaPlayerListener>& listener);
	status_t prepare();
	status_t prepareAsync();
	status_t start();
	status_t stop();
	status_t pause();
	bool isPlaying();
	status_t getVideoWidth(int *w);
	status_t getVideoHeight(int *h);
	status_t seekTo(int msec);
	status_t getCurrentPosition(int *msec);
	status_t getDuration(int *msec);
	status_t reset();
	status_t setAudioStreamType(inttype);
	status_t setLooping(int loop);
	status_t setVolume(floatleftVolume, float rightVolume);
	void notify(int msg, int ext1, intext2);
	static sp<IMemory> decode(const char* url, uint32_t *pSampleRate, int* pNumChannels);
	static sp<IMemory> decode(int fd, int64_t offset, int64_t length, uint32_t *pSampleRate,int* pNumChannels);
}
```
在instantiate函数中，调用IServiceManager的一个函数addService，向其中增加了一个名为"media.player"的服务。
这个名为"media.player"的服务和mediaplayer.cpp中调用getService中得到的使用一样名称。因此，在这里调用addService增加服务在mediaplayer.cpp中可以按照名称"media.player"来使用。这就是使用Binder实现进程间通讯的（IPC）的作用，事实上这个MediaPlayerService类是在服务中运行的，而mediaplayer.cpp调用的功能在应用中运行，二者并不是一个进程。但是在mediaplayer.cpp却像一个进程的调用一样调用MediaPlayerService的功能。
```  
static sp<MediaPlayerBase> createPlayer(player_typeplayerType, void* cookie, notify_callback_f notifyFunc)
{
	sp<MediaPlayerBase> p;
	switch (playerType) {
		case PV_PLAYER:
			LOGV(" create PVPlayer");
			p = newPVPlayer();
			break;
		case SONIVOX_PLAYER:
			LOGV(" create MidiFile");
			p = newMidiFile();
			break;
		case VORBIS_PLAYER:
			LOGV(" create VorbisPlayer");
			p = newVorbisPlayer();
			break;
	}
	return p;
}
```
在这里根据playerType的类型建立不同的播放器：对于大多数情况，类型将是PV_PLAYER，这时会调用了new PVPlayer()建立一个PVPlayer，然后将其指针转换成MediaPlayerBase来使用；对于Mini文件的情况，类型为SONIVOX_PLAYER，将会建立一个MidiFile； 对于Ogg Vorbis格 式的情况，将会建立一个VorbisPlayer。