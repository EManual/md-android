应用程序中不同类型的Surface,在FrameWorks本地层的SurfaceFlinger中，分别对应着不同的Layer类，本文主要是讨论这几种Layer的实现和差异。
效果图：
![img](P)  
Layer对应普通的窗口。
LayerDim 会使他后面的窗口产生一个变暗的透明效果。 
LayerBlur在LayerDim的基础上，背景会产生模糊的效果。 
#### 创建Layer
默认地，创建普通的窗口Surface，在SurfaceFlinger中会创建Layer类，如果想创建LayerDim或LayerBlur，应用程序需要在绑定View之前设置一下窗口的标志位：
创建LayerDim效果：
```  
@Override
protected void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	// 该系统模糊任何窗户后面这一个。
	getWindow().setFlags(WindowManager.LayoutParams.FLAG_DIM_BEHIND, WindowManager.LayoutParams.FLAG_DIM_BEHIND);
	......
	setContentView(......);
}
```
创建LayerBlur效果：
```  
@Override
protected void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	getWindow().setFlags(WindowManager.LayoutParams.FLAG_BLUR_BEHIND, WindowManager.LayoutParams.FLAG_BLUR_BEHIND);
	......
	setContentView(......);
}
```
相应地，在SufaceFlinger中，会根据Java层传入的标志，创建不同的Layer:
```  
sp<ISurface> SurfaceFlinger::createSurface(ClientID clientId, int pid, const String8& name, ISurfaceFlingerClient::surface_data_t* params, DisplayID d, uint32_t w, uint32_t h, PixelFormat format, uint32_t flags)
{
	sp<LayerBaseClient> layer;
	sp<LayerBaseClient::Surface> surfaceHandle;
	......
	switch (flags & eFXSurfaceMask) {
		case eFXSurfaceNormal:
			if (UNLIKELY(flags & ePushBuffers)) {
				layer = createPushBuffersSurfaceLocked(client, d, id,
				w, h, flags);
			} else {
				layer = createNormalSurfaceLocked(client, d, id,
				w, h, flags, format);
			}
		break;
		case eFXSurfaceBlur:
			layer = createBlurSurfaceLocked(client, d, id, w, h, flags);
		break;
		case eFXSurfaceDim:
			layer = createDimSurfaceLocked(client, d, id, w, h, flags);
		break;
	}
	if (layer != 0) {
		layer->setName(name);
		setTransactionFlags(eTransactionNeeded);
		surfaceHandle = layer->getSurface();
		........
	}
	return surfaceHandle;
}
```
Layer类的静态结构
下面的图展示了Layer类之间的继承关系：
![img](P)  
所有的Layer都继承了LayerBaseClient，SurfaceFlinger统一通过LayerBaseClient类访问其他的派生Layer类。
LayerBaseClient的内嵌类Surface继承了ISurface接口，ISurface用于和SurfaceFlinger的客户端交互。
Layer和LayerBuffer都有各自的内嵌类：SurfaceLayer、SurfaceLayerBuffer，继承了LayerBaseClient的内嵌类Surface。 
LayerBuffer还有另外的内嵌类：Source，并且派生出另外两个内嵌类：BufferSource、OverlaySource。