3.3 Camera本地库libui.so
frameworks/base/libs/ui/中的Camera.cpp文件用于实现Camera.h提供的接口，其中一个重要的片段如下所示：
```  
const sp<ICameraService>& Camera::getCameraService()
{
	Mutex::Autolock _l(mLock);
	if (mCameraService.get() == 0) { 
		sp<IServiceManager> sm = defaultServiceManager();
		sp<IBinder> binder;
		do {
			binder = sm->getService(String16("media.camera"));
			if (binder != 0) 
			break; 
			LOGW("CameraService not published, waiting...");
			usleep(500000); // 0.5 s
		} while(true); 
		if (mDeathNotifier == NULL) { 
			mDeathNotifier = new DeathNotifier();
		} 
		binder->linkToDeath(mDeathNotifier);
		mCameraService = interface_cast<ICameraService>(binder);
	} 
	LOGE_IF(mCameraService==0, "no CameraService!?");
	return mCameraService; 
}
```
其中最重要的一点是binder=sm->getService(String16("media.camera"));;这个调用用来得到一个名称为"media.camera"的服务，这个调用返回值的类型为IBinder，根据实现将其转换成类型ICameraService使用。
一个函数 connect的实现 如下所示：
```  
sp<Camera> Camera::connect()
{
	sp<Camera> c = new Camera();
	const sp<ICameraService>& cs = getCameraService();
	if (cs != 0) { 
		c->mCamera = cs->connect(c);
	} 
	if (c->mCamera != 0) { 
		c->mCamera->asBinder()->linkToDeath(c);
		c->mStatus = NO_ERROR;
	} 
	return c; 
}
```
connect通过调用getCameraService得到一个 ICameraService，再通过 ICameraService的cs->connect（c）得到一个 ICamera类型的指针。 调用connect将得到一个 Camera的指针，正常情况下Camera的成员 mCamera已经初始化完成。
一个具体的函数startPreview 如下所示：
```  
status_t Camera::startPreview()
{
	return mCamera->startPreview(); 
}
```
这些操作可以直接对 mCamera来进行，它是ICamera类型的指针。
其他一些函数的实现也与setDataSource类似。
libmedia。so中的其他一些文件与头文件的名称相同，它们是：
```  
frameworks/base/libs/ui/ICameraClient.cpp
frameworks/base/libs/ui/ICamera.cpp
frameworks/base/libs/ui/ICameraService.cpp 
```
在此处，BnCameraClient和BnCameraService类虽然实现了onTransact()函数，但是由于还有纯虚函数没有实现，因此这个类都是不能实例化的。
ICameraClient。cpp中的BnCameraClient在别的地方也没有实现；而ICameraService。cpp中的BnCameraService类在别的地方被继承并实现，继承者实现了Camera服务的具体功能。
3.4 Camera服务libcameraservice.so
frameworks/base/camera/libcameraservice/ 用于实现一个Camera的服务，这个服务是继承ICameraService的具体实现。
在这里的Android。mk文件中，使用宏USE_CAMERA_STUB决定是否使用真的Camera，如果宏为真，则使用CameraHardwareStub.cpp和FakeCamera.cpp构造一个假的Camera，如果为假则使用 CameraService.cpp构造一个实际上的Camera服务。
CameraService.cpp是继承BnCameraService 的实现，在这个类的内部又定义了类Client，CameraService::Client继承了BnCamera。在运作的过程中 CameraService::connect()函数用于得到一个CameraService::Client，在使用过程中，主要是通过调用这个类的接口来实现完成Camera的功能，由于CameraService::Client本身继承了BnCamera类，而BnCamera类是继承了 ICamera，因此这个类是可以被当成ICamera来使用的。
CameraService和CameraService：：Client两个类的结果如下所示：
```  
class CameraService : public BnCameraService 
{
	class Client : public BnCamera {};
	wp<Client> mClient;
}
```
在CameraService中的一个静态函数instantiate()用于初始化一个Camera服务，寒暑如下所示：
```  
void CameraService::instantiate() { 
	defaultServiceManager()->addService( String16("media.camera"), new CameraService());
}
```
事实上，CameraService：instantiate()这个函数注册了一个名称为"media。camera"的服务，这个服务和Camera.cpp中调用的名称相对应。
Camera整个运作机制是：在Camera。cpp中可以调用ICameraService的接口，这时实际上调用的是 BpCameraService，而BpCameraService又通过Binder机制和BnCameraService实现两个进程的通讯。而 BpCameraService的实现就是这里的CameraService。因此，Camera。cpp虽然是在另外一个进程中运行，但是调用 ICameraService的接口就像直接调用一样，从connect()中可以得到一个ICamera类型的指针，真个指针的实现实际上是 CameraService：Client。
而这些Camera功能的具体实现，就是CameraService：：Client所实现的了，其构造函数如下所示：
```  
CameraService::Client::Client(const sp<CameraService>& cameraService,
const sp<ICameraClient>& cameraClient) : mCameraService(cameraService), mCameraClient(cameraClient), mHardware(0)
{
	mHardware = openCameraHardware();
	mHasFrameCallback = false;
}
```
构造函数中，调用openCameraHardware()得到一个CameraHardwareInterface类型的指针，并作为其成员mHardware。以后对实际的Camera的操作都通过对这个指针进行。这是一个简单的直接调用关系。
事实上，真正的Camera功能己通过实现CameraHardwareInterface类来完成。在这个库当中 CameraHardwareStub。h和CameraHardwareStub。cpp两个文件定义了一个桩模块的接口，在没有Camera硬件的情况下使用，例如在仿真器的情况下使用的文件就是CameraHardwareStub。cpp和它依赖的文件FakeCamera。cpp。
CameraHardwareStub类的结构如下所示：
```  
class CameraHardwareStub : public CameraHardwareInterface {
	class PreviewThread : public Thread {
	};
};
```