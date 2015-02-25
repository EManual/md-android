Android2.2之前版本的视频音频的播放默认支撑库为OpenCore。OpenCore功能非常强大，可以支持多种媒体格式，并支持扩展。当然本文将要简单介绍一下怎样对OpenCore进行视频硬件加速，以提高其视频运行效率。
OpenCore的作用简单的说就是对媒体（音频视频）数据进行解码，并输出到终端设备。音频数据解码和输出比较简单，本文重点介绍视频数据的解码和输出。为了使OpenCore的视频播放支持Android系统，谷歌定义了两套视频输出方案，一种是由硬件厂商实现硬件加速的视频输出，硬件视频输出里面可以调用硬件Overlay模块对输出的视频数据进行硬件混叠，这样输出效率会非常的高；另外一种为谷歌定义的软视频输出，该软视频输出定义为AndroidSurfaceOutput类，这种方案中系统会调用SurfaceFilnger对输出视频数据进行混叠，该混叠为软件混叠，执行效率比较低。请看文件PlayerDriver.cpp中handleSetVideoSurface方法的代码：
```  
// 试图负载设备细节部分的具体视频
if (mLibHandle != NULL) {
	VideoMioFactory f = (VideoMioFactory) ::dlsym(mLibHandle, VIDEO_MIO_FACTORY_NAME);
	if (f != NULL) {
		mio = f();
	}
}
//如果没有设备细节部分的具体被创建,使用通用
if (mio == NULL) {
	LOGW("Using generic video MIO");
	mio = new AndroidSurfaceOutput();
}
```
可以看出如果mLibHandle不为空，则调用硬件库中的MIO（多媒体IO）工厂方法产生MIO（多媒体IO）。如果mLibHandle为空，则用通用视频MIO（AndroidSurfaceOutput类）。如果你仔细研究AndroidSurfaceOutput类你会发现，其底层调用的SurfaceFlinger来进行视频数据混叠，然后输出的，这部分有兴趣的朋友可以去查看代码。
那么mLibHandle那里初始化的呢？在文件PlayerDriver.cpp中PlayerDriver类的构造函数中有如下代码：
```  
// 运行在仿真中吗?
mLibHandle = NULL;
char value[PROPERTY_VALUE_MAX];
if (property_get("ro.kernel.qemu", value, 0)) {
	mEmulation = true;
	LOGV("Emulation mode - using software codecs");
} else {
// 尝试开启h / w
	mLibHandle = ::dlopen(MIO_LIBRARY_NAME, RTLD_NOW);
	if (mLibHandle != NULL) {
		LOGV("OpenCore hardware module loaded");
	} else {
		LOGV("OpenCore hardware module not found");
	}
}
```
现在我们可以非常清楚的看到如果系统中有libopencorehw.so库，则OpenCore将会调用该库中的createVideoMio函数来创建MIO。由此我们可以知道对OpenCore的视频输出硬件加速，其实就是定义libopencorehw.so库。
这篇我们主要就是讲了OpenCore视频输出的硬件加速的原理。