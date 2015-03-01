Android的系统构架，让大家对Android系统有了一个深入的认识；但是，Android系统本身非常庞大，在深入分析每个模块的具体功能之前，有必要对其启动过程进行分析。我们需要了解这么一个庞大的系统在启动的时候需要执行哪些初始化操作。Android 系统在启动时首先会启动 Linux 基础系统，然后引导加载 Linux Kernel 并启动初始化进程（Init）。
启动Init进程
接着，启动 Linux 守护进程（daemons）。这个过程主要需要启动以下内容。启动USB守护进程（usbd）来管理USB连接。启动 Android Debug Bridge守护进程（adbd）来管理ADB连接。启动Debug守护进程（debuggerd）来管理调试进程的请求（包括内存转换等）。启动无线接口守护进程（rild）来管理无线通信。
#### 启动 Linux 守护进程
在启动 Linux守护进程的同时还需要启动Zygote进程。它主要包括以下需要启动和注册的内容：
初始化一个 Dalvik 虚拟机实例。
装载 Socket 请求所需的类和监听。
创建虚拟机实例来管理应用程序的进程。
runtime进程初始化之后，runtime进程将发送一个请求到Zygote，开始启动系统服务，这时Zygote将为系统服务进程建立一个虚拟机实例，并启动系统服务。
紧接着，系统服务将启动原生系统服务，主要包括 Surface Flinger 和 Audio Flinger。这些本地系统服务将注册到服务管理器（Service Manager）作为 IPC 服务的目标。
系统服务将启动 Android 管理服务，Android 管理服务将都被注册到服务管理器上。
最后，当系统加载完所有的服务之后会处于等待状态，等待程序运行。但是，每一个应用程序都将启动一个单独的进程。系统启动了一个 Home 进程和一个 Contacts 进程。那么，各个进程之间如何进行交互呢？这就需要使用IPC机制了，后面会详细介绍。
系统启动过程的每一步都有了深入的理解。实际上，这个启动过程就是从Android系统构架图中最底层的 Linux 内核层一步一步加载和注册到应用程序框架层，最终在应用程序层运行我们自己的应用程序。那么，各个层次之间又有什么样的关系呢？应用程序在最上层又是如何调用到最底层的核心服务的呢？下一小节将为大家分析各个层次之间的关系。
#### 各个层次之间的相互关系
Android系统的启动过程，本小节将介绍Android应用程序是如何按照层次关系来调用最底层的硬件和服务的。如果你对上一节的内容理解得还不够深入，那么可以回去再仔细地看一遍，这样才能更好地理解本小节的内容。好的，相信你已经准备好了，开始吧。在Android中运行的应用程序都是通过以下三种方式来层层深入的：
```  
App→Runtime Service→Lib
App→Runtime Service→Native Service→Lib
App→Runtime Service→Native Daemon→Lib
```
下面就分别来分析这三种方式，我们还是采用流程图的方式来为大家展示。App→Runtime Service→Lib 方式对应的流程图。
我们可以看出，在Android平台上，应用程序首先是在应用程序层通过Binder IPC调用应用程序框架层的 Runtime Service，然后再通过 JNI 与运行库中的原生服务绑定，并动态地加载 Hal 库，进而调用 Linux 内核层的 Kernel Driver。为了便于大家更好地理解，我们通过一个实例（Location Manager）来为分析该流程。
应用程序调用了应用程序框架层的 MediaPlayer，然后调用系统运行库层的 MediaPlaye。这时 MediaPlaye 又分别调用了Media Framework和 AudioFlinger，而后通过 AudioFlinger 调用指定的库（libaudio.so），最后才调用到 Kernel Driver。下面来看一下最后一种方式“App→Runtime Service→Native Daemon→Lib”。这种方式通常用于守护进程的连接。
再通过sockets调用守护进程进行动态加载。下面就来看一个简单的例子，电话管理（TelephonyManager）的调用就是这样一个原生的守护进程调用。