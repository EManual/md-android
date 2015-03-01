2.4 头文件ICamera.h
ICamera.h描述的内容是一个实现Camera功能的接口，其定义如下所示：
```  
class ICamera: public IInterface 
{ 
	public:
	DECLARE_META_INTERFACE(Camera);
	virtual void disconnect() = 0;
	virtual status_t setPreviewDisplay(const sp<ISurface>& surface) = 0;
	virtual void setHasFrameCallback(bool installed) = 0;
	virtual status_t startPreview() = 0;
	virtual void stopPreview() = 0;
	virtual status_t autoFocus() = 0;
	virtual status_t takePicture() = 0;
	virtual status_t setParameters(const String8& params) = 0;
	virtual String8 getParameters() const = 0;
};  
class BnCamera: public BnInterface<ICamera> 
{ 
	public:
	virtual status_t onTransact( uint32_t code,
	const Parcel& data,
	Parcel* reply,
	uint32_t flags = 0);
};
```
ICamera。h描述的内容是一个实现Camera功能的接口，其定义如下所示：
在camera类中，主要定义Camera的功能接口，这个类必须被继承才能够使用。值得注意的是，这些接口和Camera类的接口有些类似，但是它们并没有直接的关系。事实上，在Camera类的各种实现中，一般都会通过调用ICamera类的实现类来完成。
2.5 头文件ICameraService.h
ICameraService.h用于描述一个Camera的服务，定义方式如下所示：
```  
class ICameraService : public IInterface 
{ 
	public:
	DECLARE_META_INTERFACE(CameraService);
	virtual sp<ICamera> connect(const sp<ICameraClient>& cameraClient) = 0;
}; 
class BnCameraService: public BnInterface<ICameraService> 
{ 
	public:
	virtual status_t onTransact( uint32_t code,
	const Parcel& data,
	Parcel* reply,
	uint32_t flags = 0);
};
```
由于具有纯虚函数，ICameraService以及BnCameraService必须被继承实现才能够使用，在ICameraService只定义了一个connect()接口，它的返回值的类型是sp<ICamera>，这个ICamera 是提供实现功能的接口。注意，ICameraService只有连接函数connect()，没有断开函数，断开的功能由ICamera接口来提供。
2.6 头文件CameraHardwareInterface.h
CameraHardwareInterface。h定义的是一个Camera底层的接口，这个类的实现者是最终实现Camera的。
CameraHardwareInterface 定以Camera硬件的接口，如下所示：
```  
class CameraHardwareInterface : public virtual RefBase { 
	public:
	virtual ~CameraHardwareInterface() { }
	virtual sp<IMemoryHeap> getPreviewHeap() const = 0;
	virtual status_t startPreview(preview_callback cb, void* user) = 0;
	virtual void stopPreview() = 0;
	virtual status_t autoFocus(autofocus_callback,
	void* user) = 0;
	virtual status_t takePicture(shutter_callback,
	raw_callback,
	jpeg_callback,
	void* user) = 0;
	virtual status_t cancelPicture(bool cancel_shutter,
	bool cancel_raw,
	bool cancel_jpeg) = 0;
	virtual status_t setParameters(const CameraParameters& params) = 0;
	virtual CameraParameters getParameters() const = 0;
	virtual void release() = 0;
	virtual status_t dump(int fd, const Vector<String16>& args) const = 0;
};
```
使用C语言的方式导出符号：
extern "C" sp<CameraHardwareInterface> openCameraHardware()；
在程序的其他地方，使用openCameraHardware()就可以得到一个 CameraHardwareInterface，然后调用 CameraHardwareInterface的接口完成Camera的功能。