#### 06_SharedClient:客户端（Surface）和服务端（Layer）
SurfaceFlinger在系统启动阶段作为系统服务被加载。应用程序中的每个窗口，对应本地代码中的Surface，而Surface又对应于SurfaceFlinger中的各个Layer，SurfaceFlinger的主要作用是为这些Layer申请内存，根据应用程序的请求管理这些 Layer显示、隐藏、重画等操作，最终由SurfaceFlinger把所有的Layer组合到一起，显示到显示器上。当一个应用程序需要在一个 Surface上进行画图操作时，首先要拿到这个Surface在内存中的起始地址，而这块内存是在SurfaceFlinger中分配的，因为 SurfaceFlinger和应用程序并不是运行在同一个进程中，如何在应用客户端（Surface）和服务端（SurfaceFlinger – Layer）之间传递和同步显示缓冲区？这正是本文要讨论的内容。Surface的创建过程
我们来看看效果图：
![img](P)  
创建Surface的过程基本上分为两步：
1. 建立SurfaceSession
第一步通常只执行一次，目的是创建一个SurfaceComposerClient的实例，JAVA层通过JNI调用本地代码，本地代码创建一个SurfaceComposerClient的实例，SurfaceComposerClient通过ISurfaceComposer接口调用SurfaceFlinger的createConnection，SurfaceFlinger返回一个ISurfaceFlingerClient接口给SurfaceComposerClient，在createConnection的过程中，SurfaceFlinger创建了用于管理缓冲区切换的SharedClient，关于SharedClient我们下面再介绍，最后，本地层把SurfaceComposerClient的实例返回给 JAVA层，完成SurfaceSession的建立。
2. 利用SurfaceSession创建Surface
JAVA层通过JNI调用本地代码Surface_Init()，本地代码首先取得第一步创建的SurfaceComposerClient实例，通过SurfaceComposerClient，调用ISurfaceFlingerClient接口的createSurface方法，进入 SurfaceFlinger，SurfaceFlinger根据参数，创建不同类型的Layer，然后调用Layer的setBuffers()方法，为该Layer创建了两个缓冲区，然后返回该Layer的ISurface接口，SurfaceComposerClient使用这个ISurface接口创建一个SurfaceControl实例，并把这个SurfaceControl返回给JAVA层。
```  
void SurfaceComposerClient::_init(
const sp<ISurfaceComposer>& sm, const sp<ISurfaceFlingerClient>& conn)
{
	......
	mClient = conn;
	if (mClient == 0) {
		mStatus = NO_INIT;
		return;
	}
	mControlMemory = mClient->getControlBlock();
	mSignalServer = sm;
	mControl = static_cast<SharedClient *>(mControlMemory->getBase());
}
```
SharedClient
在createConnection阶段，SurfaceFlinger创建Client对象：
```  
sp<ISurfaceFlingerClient> SurfaceFlinger::createConnection()
{
	Mutex::Autolock _l(mStateLock);
	uint32_t token = mTokens.acquire();
	sp<Client> client = new Client(token, this);
	if (client->ctrlblk == 0) {
		mTokens.release(token);
		return 0;
	}
	status_t err = mClientsMap.add(token, client);
	if (err < 0) {
		mTokens.release(token);
		return 0;
	}
	sp<BClient> bclient =
	new BClient(this, token, client->getControlBlockMemory());
	return bclient;
}
```
再进入Client的构造函数中，它分配了4K大小的共享内存，并在这块内存上构建了SharedClient对象：
```  
Client::Client(ClientID clientID, const sp<SurfaceFlinger>& flinger)
: ctrlblk(0), cid(clientID), mPid(0), mBitmap(0), mFlinger(flinger)
{
	const int pgsize = getpagesize();
	const int cblksize = ((sizeof(SharedClient)+(pgsize-1))&~(pgsize-1));
	mCblkHeap = new MemoryHeapBase(cblksize, 0,
	"SurfaceFlinger Client control-block");
	ctrlblk = static_cast<SharedClient *>(mCblkHeap->getBase());
	if (ctrlblk) { // construct the shared structure in-place.
		new(ctrlblk) SharedClient;
	}
}
```