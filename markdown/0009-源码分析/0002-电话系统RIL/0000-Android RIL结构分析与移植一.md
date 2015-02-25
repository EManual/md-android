Android RIL结构分析与移植
#### 介绍
本文档对Android RIL部分的内容进行了介绍，其重点放在了Android RIL的原生代码部分。包括四个主题：
```  
1.Android RIL框架介绍。
2.Android RIL与 WindowsMobile RIL。
3.Android RIL porting。
4.Android RIL的java框架。
```
在本文档中将Android代码中的重要模块列出进行分析，并给出了相关的程序执行流程介绍，以加深对模块间交互方式的理解。
对于java代码部分，这里仅进行简单的介绍。如果需要深入了解，可以查看相关参考资料。
本文档中还对Android RIL的Porting部分内容进行了描述和分析。
针对对unix操作系统环境并不熟悉的读者，本文档中所涉及到的相关知识包括：
```  
Unix file system
Unix socket
Unix thread
Unix 下I/O多路转接
```
以上信息可以在任意一份描述Unix系统调用的文档中找到。
#### 1.Android RIL框架介绍
术语：
fd
unix文件描述符
pipe
unix管道
cond
一般是condition variable的缩写
tty
通常使用tty来简称各种类型的终端设备
unsolicited response
被动请求命令来自baseband
event loop
android的消息队列机制，由unix的系统调用select()实现
init.rc
init守护进程启动后被执行的启动脚本。
HAL
硬件抽象层(Hardware Abstraction Layer，HAL）
#### 1.1.Android RIL概况：
Android RIL提供了无线硬件设备与电话服务之间的抽象层。
下图展示了RIL在Android体系中的位置。
![img](P)  
```  
<v:shape 
	style="WIDTH: 414.75pt;
	HEIGHT: 385.5pt;
	VISIBILITY: visible;
	mso-wrap-style: square"
	id=图片_x0020_5 o:button="t"
	target="_blank"
	href="http://photo.blog.sina.com.cn/showpic.htmlblogid=682793a50100jeo5&url=http://static2.photo.sina.com.cn/orignal/682793a5t899935340821"
	alt="Android RIL结构分析与移植(1)" type="_x0000_t75" o:spid="_x0000_i1025">
<v:imagedata o:title="Android RIL结构分析与移植(1)" 
	src="file:///C:\Users\LINJAC~1\AppData\Local\Temp\msohtmlclip1\01\clip_image001.gif">
```
android的ril位于应用程序框架与内核之间，分成了两个部分，一个部分是rild,它负责socket与应用程序框架进行通信。另外一个部分是Vendor RIL，这个部分负责向下是通过两种方式与radio进行通信，它们是直接与radio通信的AT指令通道和用于传输包数据的通道，数据通道用于手机的上网功能。
对于RIL的java框架部分，也被分成了两个部分，一个是RIL模块，这个模块主要用于与下层的rild进行通信，另外一个是Phone模块，这个模块直接暴露电话功能接口给应用开发用户，供他们调用以进行电话功能的实现。
#### 1.2.Android RIL目录结构:
Android的RIL模块位于Android/hardware/ril文件夹，有三个子模块：rild，libril，reference-ril
●include文件夹：
包含RIL头文件，最主要的是ril.h
●rild文件夹:
RIL守护进程，开机时被init守护进程调用启动，里面仅有main函数作为入口点，负责完成RIL初始化工作。
在rild.c文件中，将完成ril的加载过程，它会执行如下操作：
动态加载Vendor RIL的.so文件
执行RIL_startEventLoop()开启消息队列以进行事件监听
通过执行Vendor RIL的rilInit()方法来进行Vendor RIL与libril的关系建立。
在rild文件夹中还包括一个radiooptions.c文件,它的作用是通过串口将一些radio相关的参数直接传给rild来对radio进行配置。
●libril文件夹：
在编译时libril被链入rild,它为rild提供了event处理功能，还提供了在rild与Vendor RIL之间传递请求和响应消息的能力。
Libril提供的主要功能分布在两个主要方法内，一个是RIL_startEventLoop()方法，另一个是RIL_register()方法
RIL_startEventLoop()方法所提供的功能就是启用eventLoop线程，开始执行RIL消息队列。
RIL_register()方法的主要功能是启动名为 rild 的监听端口，等待java 端通过socket进行连接。
●reference-ril文件夹：
Android自带的Vendor RIL的参考实现。被编译成.so文件，由于本部分是厂商定制的重点所在。所以被设计为松散耦合，且可灵活配置的。在rild中通过opendl()的方式加载。
librefrence.so负责直接与radio通信，这包括将来自libril的指令转换为AT指令，并且将AT指令写入radio中。
reference-ril会接收调用者传来的参数，参数内容为与radio的通信方式。如通过串口连接radio，那么参数为这种形式：-d /dev/ttySx
#### 1.3.Android RIL中的消息(event）队列机制:
在Android RIL中，为了达到等待多路输入并且不出现阻塞的目的，使用了IO多路复用机制。
如果使用阻塞I/O进行网络的读取写入,这意味着假如需要同时从两个网络文件描述符中读内容，那么如果读取操作在等待网络数据到来，这将可能很长时间阻塞在一个描述符上，另一个网络文件描述符不管有没有数据到来都无法被读取。
一种解决方案是：
如果使用非阻塞I/O进行网络的读取写入，在读取其中一个网络文件描述符如果阻塞将直接返回，再读取另外一个，这种方式的循环被称之为轮询。轮询方式确实能解决进行多路io操作时的阻塞问题，但是这种方法的不足之处是反复的执行读写调用将浪费cpu时钟。
I/O多路转接技术在这里提供了另一种比较好的解决方案：
它会先构造一张有关I/O描述符的列表，然后调用select函数，当IO描述符列表中的一个描述符准备好进行I/O时，该函数返回，并告知可以读或写哪个描述符。
Android RIL中消息队列的核心实现思想就是这种I/O多路转接技术。
消息队列机制的实现在ril_event.cpp中，其中被定义的ril_event结构是消息的主体。
每个ril_event结构，与一个fd句柄绑定(可以是文件，socket，管道等)，并且带一个func指针,这个func指针所指的函数是个回调函数，它指定了当所绑定的fd准备好进行读取时所要进行的操作。
消息队列的开始点为RIL_startEventLoop函数。RIL_startEventLoop在ril.cpp中实现，它的主要目的是通过pthread_create(&s_tid_dispatch, &attr, eventLoop, NULL)建立一个dispatch线程，线程入口点在eventLoop. 而在eventLoop中，会调ril_event.cpp中的ril_event_loop()函数，建立起消息队列机制。
ril_event是一个带有链表行为的struct，它最主要的成员一个是fd,一个是func：
```  
struct ril_event {
	struct ril_event *next;
	struct ril_event *prev;
	int fd;
	int index;
	bool persist;
	struct timeval timeout;
	ril_event_cb func;
	void *param;
};
```
初始化一个新ril_event的操作是通过ril_event_set(）来完成的，并通过ril_event_add(）加入到消息队列之中，add会把队列里所有ril_event的fd，放入一个fd集合readFds中。这样 ril_event_loop能通过一个多路复用I/O的机制(select)来等待这些fd。
在进入ril_event_loop(）之前，在eventLoop中已经创建和挂入了s_wakeupfd_event，它是通过pipe的机制实现的，这个管道fd的回调函数并没有实现什么功能，它的目的只是为了让select方法能返回一次，这样select()方法就能重新跟踪新加入事件队列的fd和timeout设置。
所以在添加新fd到eventLoop时,往往不是直接调用ril_event_add，实际通常用rilEventAddWakeup来添加，这个方法除了会间接调用ril_event_add外，还会调用triggerEvLoop()函数来向s_fdWakeupWrite中写入一个空字符，这样select()函数会返回并重新执行，新加入的文件描述符便得以被select()加载并跟踪。
如果在ril_event队列中任何一个fd已经准备好，则进入分析流程：
processTimeouts()，processReadReadies(&rfds, n)，firePending().其中firePending()方法执行这个event的func，也就是回调函数。
在Android RIL初始化完成后，将有几个event被挂入到eventLoop中：
1.s_listen_event: 名为rild的socket,主要requeset & response通道
2.s_debug_event: 名为rild-debug的socket,调试用requeset & response通道
3.s_wakeupfd_event: 无名管道,用于队列主动唤醒
这其中最为重要的event就是s_listen_event，它作为request与response的通道实现。
在ril_event.cpp中还持有一个watch_table数组，一个timer_list链表和一个pending_list链表。
watch_table数组的目的很单纯，存放当前被eventLoop等待的ril_event(非timer event），供eventLoop唤醒时使用。
timer_list是存放timer event的链表，在eventLoop唤醒时要对这些timer event单独进行处理。
pending_list：待处理(对其回调函数进行调用)的所有ril_event的链表。