#### Android堆内存也可自己定义大小
对于一些大型Android项目或游戏来说在算法处理上没有问题外，影响性能瓶颈的主要是Android自己内存管理机制问题，目前手机厂商对RAM都比较吝啬，对于软件的流畅性来说RAM对性能的影响十分敏感，除了上次eoeAndroid提到的优化Dalvik虚拟机的堆内存分配外，我们还可以强制定义自己软件的对内存大小，我们使用Dalvik提供的 dalvik.system.VMRuntime类来设置最小堆内存为例:
```  
private final static
int CWJ_HEAP_SIZE = 6* 1024* 1024 ;
VMRuntime.getRuntime().setMinimumHeapSize(CWJ_HEAP_SIZE); //设置最小heap内存为6MB大小。
private final static int CWJ_HEAP_SIZE = 6* 1024* 1024 ; 
VMRuntime.getRuntime().setMinimumHeapSize(CWJ_HEAP_SIZE); //设置最小heap内存为6MB大小。
```
当然对于内存吃紧来说还可以通过手动干涉GC去处理，我们将在下次提到具体应用。
优化Dalvik虚拟机的堆内存分配。对于Android平台来说，其托管层使用的Dalvik JavaVM从目前的表现来看还有很多地方可以优化处理，比如我们在开发一些大型游戏或耗资源的应用中可能考虑手动干涉GC处理，使用dalvik.system.VMRuntime类提供的setTargetHeapUtilization方法可以增强程序堆内存的处理效率。当然具体原理我们可以参考开源工程，这里我们仅说下使用方法:
```  
private final static floatTARGET_HEAP_UTILIZATION = 0.75f;
```
在程序onCreate时就可以调用
```  
VMRuntime.getRuntime().setTargetHeapUtilization(TARGET_HEAP_UTILIZATION);
```
android系统中读取位图Bitmap时.分给虚拟机中图片的堆栈大小只有8M。所以不管是如何调用的图片，太多太大虚拟机肯定会报那个错误。超出图片内存预算那个错误.:java.lang.OutOfMemoryError: bitmap size exceeds VM budget。
遇到这个问题是因为没有回收资源。
```  
public void distoryBitmap(){ 
	if(null!=bmb&&!bmb.isRecycled())
		bmb.recycle();
} 
```
调用上面的代码可以基本解决这个问题.但是千万不要在view中的onDraw()中调用.因为onDraw()方法是系统循环调用。只要图片打开。
系统就不停的调用该方法。
最好的解决方案是在自定义的View中添加一个init()初始化方法的头部调用.或者在构造函数的顶部调用。