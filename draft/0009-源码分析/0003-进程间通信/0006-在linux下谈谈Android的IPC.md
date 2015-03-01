我们理解了Service Manager的工作就是登记功能，现在再回到IPC上，客服端如何建立连接的？我们首先回到通讯的本质：IPC。从一般的概念来讲，Android设计者在Linux内核中设计了一个叫做Binder的设备文件，专门用来进行Android的数据交换。所有从数据流来看Java对象从Java的VM空间进入到C++空间进行了一次转换，并利用C++空间的函数将转换过的对象通过driver\binder设备传递到服务进程，从而完成进程间的IPC。这个过程可以用下图来表示。
![img](P)  
这里数据流有几层转换过程。
(1) 从JVM空间传到c++空间，这个是靠JNI使用ENV来完成对象的映射过程。
(2) 从c++空间传入内核Binder设备，使用ProcessState类完成工作。
(3) Service从内核中Binder设备读取数据。
Android设计者需要利用面向对象的技术设计一个框架来屏蔽掉这个过程。要让上层概念空间中没有这些细节。Android设计者是怎样做的呢？我们通过c++空间代码分析。
![img](P)  
在ProcessState类中包含了通讯细节，利用open_binder打开Linux设备dev\binder,通过ioctrl建立的基本的通讯框架。利用上层传递下来的servicehandle来确定请求发送到那个Service。通过分析我终于明白了Bnbinder，BpBinder的命名含义，Bn-代表Native，而Bp代表Proxy。一旦理解到这个层次，ProcessState就容易弄明白了。
下面我们看JVM概念空间中对这些概念的包装。为了通篇理解设备上下文，我们需要将Android VM概念空间中的设备上下文和C++空间总的设备上下文连接起来进行研究。
为了在上层使用统一的接口，在JVM层面有两个东西。在Android中，为了简化管理框架，引入了ServiceManger这个服务。所有的服务都是从ServiceManager开始的，只用通过Service Manager获取到某个特定的服务标识构建代理IBinder。在Android的设计中利用Service Manager是默认的Handle为0，只要设置请求包的目标句柄为0，就是发给Service Manager这个Service的。在做服务请求时，Android建立一个新的Service Manager Proxy。Service Manager Proxy使用ContexObject作为Binder和Service Manager Service(服务端)进行通讯。
我们看到Android代码一般的获取Service建立本地代理的用法如下：
IXXX  mIxxx=IXXXInterface.Stub.asInterface(ServiceManager.getService("xxx"));
使用输入法服务：
IInputMethodManager mImm=IInputMethodManager.Stub.asInterface(ServiceManager.getService("input_method"));
这些服务代理获取过程分解如下：
(1) 通过调用GetContextObject调用获取设备上下对象。注意在AndroidJVM概念空间的ContextObject只是与Service Manger Service通讯的代理Binder有对应关系。这个跟c++概念空间的GetContextObject意义是不一样的。
```  
BinderInternal.getContextObject() @BinderInteral.java
NATIVE JNI:getContextObject() @android_util_Binder.cpp
Android_util_getConextObject @android_util_Binder.cpp
ProcessState::self()->getCotextObject(0) @processState.cpp
getStrongProxyForHandle(0) @
NEW BpBinder(0)	
```
(2)通过调用ServiceManager.asInterface(ContextObject)建立一个代理ServiceManger。
mRemote= ContextObject(Binder)这样就建立起来ServiceManagerProxy通讯框架。
(3)客户端通过调用ServiceManager的getService的方法建立一个相关的代理Binder。
```  
ServiceMangerProxy.remote.transact(GET_SERVICE)
IBinder=ret.ReadStrongBinder() -》这个就是JVM空间的代理Binder
JNI Navite: android_os_Parcel_readStrongBinder() @android_util_binder.cpp
Parcel->readStrongBinder() @pacel.cpp
unflatten_binder @pacel.cpp
getStrongProxyForHandle(flat_handle)
NEW BpBinder(flat_handle)-》这个就是底层c++空间新建的代理Binder。	
```
效果图：
![img](P)  