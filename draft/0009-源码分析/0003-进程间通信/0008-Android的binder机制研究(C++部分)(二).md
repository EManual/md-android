#### (2)定义和实现binder类
binder类包括两个，一个是接口实现类，一个接口代理类。接口代理类继承自BpInterface，接口实现类继承自BnInterface。这两个基类都是模板类，封装了binder的进程间通信机制，这样使用者无需关注底层通信实现细节。
对于IMediaPlayerService接口，其binder接口实现类为BnMediaPlayerService，接口代理类为BpMediaPlayerService。需注意这两个类的名称有严格要求，必须以Bn和Bp开头，并且后面的部分必须是前面所定义的接口类的名称去除字母'I’。比如前面所定义的接口类为IMediaPlayerService，去除字母I后是MediaPlayerService，所以两个binder类的名称分别是BnMediaPlayerService和BpMediaPlayerService。为什么有这样的要求？原因就在前面提到的宏定义DECLARE_META_INTERFACE()和另一个宏定义IMPLEMENT_META_INTERFACE()里面。有兴趣的话可以去看一下，这两个宏定义都在文件frameworks\base\include\utils\IInterface.h里。
BpMediaPlayerService是一个最终实现类。定义并且实现在在文件frameworks\base\media\libmidia\IMediaPlayerService.cpp中。在看BpMediaPlayerService的代码之前，先看一下在IMediaPlayerService.cpp文件的开始部分的一个枚举定义：
```  
enum {
	CREATE_URL = IBinder::FIRST_CALL_TRANSACTION,
	CREATE_FD,
	DECODE_URL,
	DECODE_FD,
	CREATE_MEDIA_RECORDER,
	CREATE_METADATA_RETRIEVER,
};	
```
这些6个枚举定义对应于IMediaPlayerService接口所提供的6个功能函数，可以称为这些功能函数的功能代码，用于在进程之间进行RPC是标识需要调用哪个函数。如果不想定义这些枚举值，在后面需要用到这些值的地方直接写上1，2，3，4，5，6也是可以的，不过……一个合适的程序员会这么干吗？
下面看一下BpMediaPlayerService的代码。
#### (3) BpMediaPlayerService代码分析
```  
class BpMediaPlayerService: public BpInterface<IMediaPlayerService>
{
public:
BpMediaPlayerService(const sp<IBinder>& impl) : BpInterface<IMediaPlayerService>(impl)
virtual sp<IMediaMetadataRetriever> createMetadataRetriever(pid_t pid)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeInt32(pid);
	remote()->transact(CREATE_METADATA_RETRIEVER, data, &reply);
	return interface_cast<IMediaMetadataRetriever>(reply.readStrongBinder());
}
virtual sp<IMediaPlayer> create(pid_t pid, const sp<IMediaPlayerClient>& client, const char* url)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeInt32(pid);
	data.writeStrongBinder(client->asBinder());
	data.writeCString(url);
	remote()->transact(CREATE_URL, data, &reply);
	return interface_cast<IMediaPlayer>(reply.readStrongBinder());
}
virtual sp<IMediaRecorder> createMediaRecorder(pid_t pid)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeInt32(pid);
	remote()->transact(CREATE_MEDIA_RECORDER, data, &reply);
	return interface_cast<IMediaRecorder>(reply.readStrongBinder());
}
virtual sp<IMediaPlayer> create(pid_t pid, const sp<IMediaPlayerClient>& client, int fd, int64_t offset, int64_t length)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeInt32(pid);
	data.writeStrongBinder(client->asBinder());
	data.writeFileDescriptor(fd);
	data.writeInt64(offset);
	data.writeInt64(length);
	remote()->transact(CREATE_FD, data, &reply);
	return interface_cast<IMediaPlayer>(reply.readStrongBinder());
}
virtual sp<IMemory> decode(const char* url, uint32_t *pSampleRate, int* pNumChannels, int* pFormat)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeCString(url);
	remote()->transact(DECODE_URL, data, &reply);
	*pSampleRate = uint32_t(reply.readInt32());
	*pNumChannels = reply.readInt32();
	*pFormat = reply.readInt32();
	return interface_cast<IMemory>(reply.readStrongBinder());
}
virtual sp<IMemory> decode(int fd, int64_t offset, int64_t length, uint32_t *pSampleRate, int* pNumChannels, int* pFormat)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeFileDescriptor(fd);
	data.writeInt64(offset);
	data.writeInt64(length);
	remote()->transact(DECODE_FD, data, &reply);
	*pSampleRate = uint32_t(reply.readInt32());
	*pNumChannels = reply.readInt32();
	*pFormat = reply.readInt32();
	return interface_cast<IMemory>(reply.readStrongBinder());
}
};	
```
首先可以看到，这个类继承自模板类BpInterface，指定类型为接口类IMediaPlayerService。BpInterface模板类定义在文件IInterface.h。看一下BpInterface的定义就可以发现，BpMediaPlayerService这样定义了以后，事实上间接继承了IMediaPlayerService，从而可以提供IMediaPlayerService接口所定义的接口函数。BpMediaPlayerService需要实现这些接口函数。在一个简单的构造函数之后，就是这些接口函数的实现。可以看到，所有的接口函数的实现方法都是一致的，都是通过binder所提供的机制将参数仍给binder的实现类，并获取返回值。这也就是这个类之所以成为代理类的原因。下面具体看一下一个接口函数。这里选的是函数create(url)。
```  
virtual sp<IMediaPlayer> create(pid_t pid, const sp<IMediaPlayerClient>& client, const char* url)
{
	Parcel data, reply;
	data.writeInterfaceToken(IMediaPlayerService::getInterfaceDescriptor());
	data.writeInt32(pid);
	data.writeStrongBinder(client->asBinder());
	data.writeCString(url);
	remote()->transact(CREATE_URL, data, &reply);
	return interface_cast<IMediaPlayer>(reply.readStrongBinder());
}	
```