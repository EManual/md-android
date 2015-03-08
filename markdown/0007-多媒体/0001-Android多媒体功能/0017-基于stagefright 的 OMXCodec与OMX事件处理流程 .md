学习了解Mutilmedia Framework有一段时间了，今天闲下来稍微整理整理。OMXCodec.cpp类属于libstagefright，在整个MM PF相当OMX的适配层，供awesomeplayer调用，而OMX.cpp，OMXNoteInstance.cpp等相当于OpenMax中的OpenMax IL,首先讲下OMXCodec与OMX callback事件的处理流程。先看整个流程的时序图吧：

![img](http://emanual.github.io/md-android/img/media_media/18_media.jpg) 

从时序图看，首先我们要建立个OMXCodecObserver,该类是OMXCodec的内部类，在create函数中被创建，并把对应的OMXCodec加入都自己的观察范围内，具体代码如下：
```  
framework/base/media/libstagefright/OMXCodec.cpp
sp<MediaSource> OMXCodec::Create(
	const sp<IOMX> &omx,
	const sp<MetaData> &meta, bool createEncoder,
	const sp<MediaSource> &source,
	const char *matchComponentName,
	uint32_t flags) {
	.....
	 sp<OMXCodecObserver> observer = new OMXCodecObserver;
	...............
	observer->setCodec(codec);
	...................
}
```
其次初始化它的callback事件和事件的派发处理函数
OMX主要的callback事件有哪些呢？在framework/base/media/libstagefright/omx/OMXNodeInstance.cpp中的kCallbacks函数有如下定义：
```  
// static
OMX_CALLBACKTYPE OMXNodeInstance::kCallbacks = {
    &OnEvent, &OnEmptyBufferDone, &OnFillBufferDone
};
```
callback在哪定义呢？看framework/base/media/libstagefright/omx/OMX.cpp中的
```  
status_t OMX::allocateNode(
.......................       
    OMXNodeInstance *instance = new OMXNodeInstance(this, observer);
    OMX_COMPONENTTYPE *handle;
    OMX_ERRORTYPE err = mMaster->makeComponentInstance(
            name, &OMXNodeInstance::kCallbacks,
            instance, &handle);
..............
mDispatchers.add(*node, new CallbackDispatcher(instance));
...................
}
```
即每个component对应一组callback事件。
这些callback由哪些函数返回呢？具体的定义在framework/base/media/libstagefright/openmax/OMX_Core.h
```  
callback EventHandler()
define OMX_SendCommand(                                  
	 hComponent,
	 Cmd,
	 nParam,
	 pCmdData)
 ((OMX_COMPONENTTYPE*)hComponent)->SendCommand(
	 hComponent,
	 Cmd,
	 nParam,
	 pCmdData)
EmptyBufferDone call back.
define OMX_EmptyThisBuffer(                              
        hComponent,
        pBuffer)
    ((OMX_COMPONENTTYPE*)hComponent)->EmptyThisBuffer(
        hComponent,
        pBuffer) /* Macro End */
FillBufferDone call back
define OMX_FillThisBuffer(                               
        hComponent,
        pBuffer)
    ((OMX_COMPONENTTYPE*)hComponent)->FillThisBuffer(
        hComponent,
        pBuffer) /* Macro End */
```
有了callback事件，如何dispatch呢？其实我们在allocateNote函数已经定义好了我们的dispatch函数
```  
mDispatchers.add(*node, new CallbackDispatcher(instance));
```
有了oberser, callback event , callbackdispatcher，那么一个callback event如何从OMX传到OMXCodec呢？下面我们以emptybuffer流程来具体看下，时序图如下：

![img](http://emanual.github.io/md-android/img/media_media/18_media2.jpg) 