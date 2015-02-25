Android的服务管理器是一个单独的进程，也向外提供接口。这段代码的含义，是通过Android的服务管理器的接口代理，请求调用服务管理器的checkService()接口函数，查找指定的服务(上面就是查找media.player服务)，查找成功后返回一个BpBinder类的对象实例，用于供IMediaPlayerService代理使用。这个对象BpBinder是在Parcel::readStrongBinder()函数里面创建的。那么到底是怎么创建出来的呢？在这里没有必要追到ServiceManager的实现代码里去，毕竟我们只是想知道BpBinder的对象是如何创建的，我们可以换一个例子来看。回到前面的BpMediaPlayerService::create()函数的实现，是不是很眼熟。没错，在那个函数里也创建了一个BpBinder类对象，那个对象是是给IMediaPlayer接口代理使用的。虽然接口不同，但是创建原理是一样的。我们继续，下面该到binder的另一个类——实现类的代码了。
#### (3)BnMediaPlayerService代码分析
BnMediaPlayerService类的定义在文件frameworks\base\include\media\IMediaPlayService.h，实现则与BpMediaPlayerService一样是在文件frameworks\base\media\libmidia\IMediaPlayerService.cpp中。类定义的代码如下：
```  
class BnMediaPlayerService: public BnInterface<IMediaPlayerService>
{
	public:
	virtual status_t onTransact( uint32_t code,
	const Parcel& data,
	Parcel* reply,
	uint32_t flags = 0);
};	
```
这个类继承自BnInterface模板类，约束类型为IMediaPlayerService。看一下BnInterface模板类的定义(IInterface.h)就可以知道，BnMediaPlayerService间接继承了IMediaPlayerService接口类。不过BnInterface类并没有实现IMediaPlayerService所定义的6个接口函数，因此BnInterface还是一个纯虚类。这些接口需要在BnMediaPlayerService的子类中真正实现，这个子类就是MediaPlayerService(frameworks\base\media\libmidiaservice\MediaPlayerService.h，frameworks\base\media\libmidiaservice\MediaPlayerService.cpp)。在BnMediaPlayerService的成员函数onTransact()中，需要调用这6个接口函数。BnMediaPlayerService中主要就是定义并实现了onTransact()函数。当在代理那边调用了transact()函数后，这边的onTransact()函数就会被调用。BnMediaPlayerService的实现代码如下：
```  
define CHECK_INTERFACE(interface, data, reply)
do { 
	if (!data.enforceInterface(interface::getInterfaceDescriptor())) { 
		LOGW("Call incorrectly routed to " interface);
		return PERMISSION_DENIED; 
	} 
} while (0)
	status_t BnMediaPlayerService::onTransact(
	uint32_t code, const Parcel& data, Parcel* reply, uint32_t flags)
{
switch(code) {
case CREATE_URL: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	pid_t pid = data.readInt32();
	sp<IMediaPlayerClient> client = interface_cast<IMediaPlayerClient>(data.readStrongBinder());
	const char* url = data.readCString();
	sp<IMediaPlayer> player = create(pid, client, url);
	reply->writeStrongBinder(player->asBinder());
	return NO_ERROR;
} 
break;
case CREATE_FD: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	pid_t pid = data.readInt32();
	sp<IMediaPlayerClient> client = interface_cast<IMediaPlayerClient>(data.readStrongBinder());
	int fd = dup(data.readFileDescriptor());
	int64_t offset = data.readInt64();
	int64_t length = data.readInt64();
	sp<IMediaPlayer> player = create(pid, client, fd, offset, length);
	reply->writeStrongBinder(player->asBinder());
	return NO_ERROR;
} 
break;
case DECODE_URL: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	const char* url = data.readCString();
	uint32_t sampleRate;
	int numChannels;
	int format;
	sp<IMemory> player = decode(url, &sampleRate, &numChannels, &format);
	reply->writeInt32(sampleRate);
	reply->writeInt32(numChannels);
	reply->writeInt32(format);
	reply->writeStrongBinder(player->asBinder());
	return NO_ERROR;
} 
break;
case DECODE_FD: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	int fd = dup(data.readFileDescriptor());
	int64_t offset = data.readInt64();
	int64_t length = data.readInt64();
	uint32_t sampleRate;
	int numChannels;
	int format;
	sp<IMemory> player = decode(fd, offset, length, &sampleRate, &numChannels, &format);
	reply->writeInt32(sampleRate);
	reply->writeInt32(numChannels);
	reply->writeInt32(format);
	reply->writeStrongBinder(player->asBinder());
	return NO_ERROR;
} 
break;
case CREATE_MEDIA_RECORDER: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	pid_t pid = data.readInt32();
	sp<IMediaRecorder> recorder = createMediaRecorder(pid);
	reply->writeStrongBinder(recorder->asBinder());
	return NO_ERROR;
} 
break;
case CREATE_METADATA_RETRIEVER: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	pid_t pid = data.readInt32();
	sp<IMediaMetadataRetriever> retriever = createMetadataRetriever(pid);
	reply->writeStrongBinder(retriever->asBinder());
	return NO_ERROR;
} 
break;
default:
return BBinder::onTransact(code, data, reply, flags);
}
}	
```