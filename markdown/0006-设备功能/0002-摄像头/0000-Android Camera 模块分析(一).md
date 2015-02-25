Camera主要的头文件有以下几个：
```  
ICameraClient.h
Camera.h 
ICamera.h
ICameraService.h
CameraHardwareInterface.h
```
在这些头文件Camera.h提供了对上层的接口，而其他的几个头文件都是提供一些接口类（即包含了纯虚函数的类），这些接口类必须被实现类继承才能够使用。
整个Camera在运行的时候，可以大致上分成Client和Server两个部分，它们分别在两个进程中运行，它们之间使用Binder机制实现进程间通讯。这样在客户端调用接口，功能则在服务器中实现，但是在客户端中调用就好像直接调用服务器中的功能，进程间通讯的部分对上层程序不可见。
从框架结构上来看，ICameraService.h、ICameraClient.h和ICamera.h三个类定义了MeidaPlayer的接口和架构，ICameraService.cpp和Camera.cpp两个文件用于Camera架构的实现，Camera的具体功能在下层调用硬件相关的接口来实现。
从Camera的整体结构上，类Camera是整个系统核心，ICamera类提供了Camera主要功能的接口，在客户端方面调用，CameraService是Camera服务，它通过调用实际的Camera硬件接口来实现功能。事实上，图中红色虚线框的部分都是Camera程序的框架部分，它主要利用了Android的系统的Binder机制来完成通讯。蓝色的部分通过调用Camera硬件相关的接口完成具体的Camera服务功能，其它的部分是为上层的JAVA程序提供JNI接口。在整体结构上，左边可以视为一个客户端，右边是一个可以视为服务器，二者通过Android的Bimder来实现进程间的通讯。
2.2 头文件ICameraClient.h
ICameraClient.h用于描述一个Camera客户端的接口，定义如下所示：
```  
class ICameraClient: public IInterface 
{ 
	public:
	DECLARE_META_INTERFACE(CameraClient);
	virtual void shutterCallback() = 0;
	virtual void rawCallback(const sp<IMemory>& picture) = 0;
	virtual void jpegCallback(const sp<IMemory>& picture) = 0;
	virtual void frameCallback(const sp<IMemory>& frame) = 0;
	virtual void errorCallback(status_t error) = 0;
	virtual void autoFocusCallback(bool focused) = 0;
};

class BnCameraClient: public BnInterface<ICameraClient> 
{ 
	public:
	virtual status_t onTransact( uint32_t code,
	const Parcel& data,
	Parcel* reply,
	uint32_t flags = 0);
};
```
在定义中，ICameraClient 类继承IInterface，并定义了一个Camera客户端的接口，BnCameraClient 继承了BnInterface<ICameraClient>，这是为基于Android的基础类Binder机制实现在进程通讯而构建的。根据BnInterface类模版的定义BnInterface<ICameraClient>类相当于双继承了BnInterface和 ICameraClient。
IcameraClient这个类的主要接口是几个回调函数shutterCallback、rawCallback和jpegCallback等，它们在相应动作发生的时候被调用。作为Camera的“客户端”，需要自己实现几个回调函数，让服务器程序去“间接地”调用它们。
2.3 头文件Camera.h
Camera.h是Camera对外的接口头文件，它被实现Camera JNI的文件android_hardware_Camera.cpp所调用。Camera.h最主要是定义了一个Camera类：
```  
class Camera : public BnCameraClient, public IBinder:: DeathRecipient 
{ 
	public:
	static sp<Camera> connect(); 
	~Camera();
	void disconnect(); 
	status_t getStatus() { return mStatus; }
	status_t setPreviewDisplay(const sp<Surface>& surface);
	status_t startPreview();
	void stopPreview(); 
	status_t autoFocus();
	status_t takePicture();
	status_t setParameters(const String8& params);
	String8 getParameters() const;
	void setShutterCallback(shutter_callback cb, void *cookie); 
	void setRawCallback(frame_callback cb, void *cookie); 
	void setJpegCallback(frame_callback cb, void *cookie); 
	void setFrameCallback(frame_callback cb, void *cookie); 
	void setErrorCallback(error_callback cb, void *cookie); 
	void setAutoFocusCallback(autofocus_callback cb, void *cookie); 
	// ICameraClient interface 
	virtual void shutterCallback();
	virtual void rawCallback(const sp<IMemory>& picture);
	virtual void jpegCallback(const sp<IMemory>& picture);
	virtual void frameCallback(const sp<IMemory>& frame);
	virtual void errorCallback(status_t error);
	virtual void autoFocusCallback(bool focused);
	//……
}
```
从接口中可以看出Camera类刚好实现了一个Camera的基本操作，例如播放（startPreview）、停止（stopPreview）、暂停（takePicture）等。在Camera类中connect()是一个静态函数，它用于得到一个Camera的实例。在这个类中，具有设置回调函数的几个函数：setShutterCallback、setRawCallback和setJpegCallback等，这几个函数是为了提供给上层使用，上层利用这几个设置回调函数，这些回调函数在相应的回调函数中调用，例如使用setShutterCallback设置的回调函数指针被 shutterCallback所调用。
在定义中，ICameraClient 类双继承了IInterface和IBinder DeathRecipient，并定义了一个Camera客户端的接口，BnCameraClient继承了BnInterface<ICameraClient>，这是为基于Android的基础类Binder机制实现在进程通讯而构建的。事实上，根据BnInterface类模版的定义BnInterface<ICameraClient>类相当于双继承了BnInterface和ICameraClient。这是Android一种常用的定义方式。
继承了DeathNotifier类之后，这样当这个类作为IBinder使用的时候，当这个Binder即将Died的时候被调用其中的binderDied函数。继承这个类基本上实现了一个回调函数的功能。