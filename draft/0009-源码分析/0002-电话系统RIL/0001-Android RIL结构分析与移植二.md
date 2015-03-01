#### 1.4.Android RIL中初始化流程分析:
●Rild的初始化流程
初始化流程从rild.c中的main函数开始，它被init守护进行调用执行：
首先在main()函数内会首先通过dlopen()函数加载Vendor RIL(在自带的参考实现中为librefrence_ril.so)。接着调用RIL_startEventLoop(）函数来启动消息队列机制。
调用librefrence_ril.so的RIL_Init()函数来进行Vendor RIL的初始化。RIL_Init()函数执行后会返回一个RIL_RadioFunction结构体，这个结构体内最重要的成员就是onRequest()方法。onRequest()方法会被dispatchFunction调用，也就是说dispatchFunction调用是程序流从rild转入Vendor RIL的分界点。
RIL_register()函数将实现两个目地，一个是将RIL_INIT中获得的RIL_RadioFunction进行注册，rild通过此种方式保证自己持有一个RIL_RadioFunction实例，第二个是将s_fdListen加入到消息队列机制中，开启s_fdListen的事件监听。
●Vendor RIL的初始化流程：
RIL_Init被调用后首先通过参数获取硬件接口的设备文件或模拟硬件接口的socket。(参见上文中对reference-ril文件夹的介绍)
接下来是创建mainLoop线程，并跳入到线程内执行。mainLoop会建立起与硬件的通信，然后通过read方法阻塞等待硬件的主动上报或响应。mainLoop还会调用initlizeCallBack()函数来向radio发送一系列的AT命令来进行radio的初始化设置工作。
#### 1.5.Android RIL中request流程分析:
上层应用开始向rild通过socket传输数据时，通过RIL消息队列机制，s_listen_event的回调函数listenCallBack将会被调用，开始进行数据流的分析与处理。
接下来,s_fdCommand = accept(s_fdListen, (sockaddr *) &peeraddr, &socklen),获取传入的socket描述符,也就是上层的java RIL传入的连接。
然后,通过record_stream_new()建立起一个RecordStream，将这个record_stream与s_fdCommand绑定，RecordStream实际上是一个用于存放数据的结构体，这个结构体提供了一些操作类来保证这个RecordStream所绑定的文件描述符被读取时里面的数据会被完整读取。
一旦s_fdCommand中有数据，它的回调函数processCommandsCallback()将会被调用，processCommandsCallback()通过record_stream_get_next阻塞读取s_fdCommand上发来的数据，直到收到一完整的request。然后将其传递进processCommandBuffer()函数，processCommandBuffer()正式进入了命令的解析部分。
每个接收到的命令将以RequestInfo的形式存在。从socket过来的数据流,是Parcel处理过的序列化字节流，在这里会通过反序列化的方法提取出来。最前面的是request号，以及token域(request的递增序列号)。request号是一个CommandInfo,它在ril_command.h中定义。
接下来，这个RequestInfo会被挂入pending的request队列，执行具体的dispatchFunction()，进行详细解析。
dispatchFunction方法有着多种实现，如dispatchVoid，dispatchString，它们的调用取决于Parcel的参数传入形式。比如说在dispatchDial方法中，Parcel对象将被解析为RIL_Dial结构。这是disptachFunction的任务之一，它的另一个任务就是调用onRequest()方法，并将解析的内容传入onRequest()方法。
从onRequest方法开始，程序控制流脱离了RILD，进入到了Vendor RIL中。
onRequest方法会通过传入的请求类型来调用指定的request×××()方法，request×××()方法则负责组装AT指令并下发给at_send_command()方法集合中的一个，这个方法集合提供了针对不同类型AT指令的实现，如单行AT指令at_send_command_singleline()，短信息指令at_send_command_sms()等。
最后，执行at_send_command_full()，再通过一个互斥的at_send_command_full_nolock()调用，完成最终的写出操作，在writeline()中，写出到初始化时打开的设备中。
需要注意的是：at_send_command_full_nolock()在将指令写入radio后并不会直接返回，而是通过条件变量等待响应信息，得到响应信息后会携带这些信息返回。具体流程可以参考下面的response流程分析。
#### 1.6.Android RIL中response流程分析:
AT的response有两种，一种是unsolicited。另一种是普通response，也就是命令的响应。
response信息的获取在readerLoop()中。由readline()函数读取上来。
读取到的line将被传入processLine()函数进行解析，processLine()函数首先会判断当前的响应是主动响应还是普通响应，如果是主动响应，将调用handleUnsolicited()函数，如果为普通响应，那么将调用handleFinalResponse()函数进行处理对响应串的主要的解析过程，由at_tok.c中的各种解析函数完成，提供字符串分析解析功能。
●对主动上报的解析
handleUnsolicited ()方法处理主动上报，它会调用onUnsolicited()来进行进一步的解析，这个函数在Vendor-RIL初始化时被传入at_open()函数，onUnsolicited只解析出头部(一般是+XXXX的形式)，然后按类型决定下一步操作，操作为 RIL_onUnsolicitedResponse和RIL_requestTimedCallback两种。
在RIL_onUnsolicitedResponse()函数中，通过Parcel传递，将RESPONSE_UNSOLICITED，unsolResponse(request号)写入Parcel，然后调用对应的responseFunction完成进一步的的解析，将解析的数据写入Parcel中，最后通过sendResponse()→sendResponseRaw()→blockingWrite()→writeLine()将数据写回给与应用层通信的socket。
在RIL_requestTimedCallback()函数中。通过event机制实现的timer机制，回调对应的内部处理函数。通过internalRequestTimedCallback将回调添加到event循环，最终完成callback上挂的函数的回调。比如pollSIMState，onPDPContextListChanged等回调，不用返回上层，内部处理就可以。
●对普通上报的解析
IsFinalResponse()和isFinalResponseError()所处理的是一条AT指令的响应上报，它们将转入handleFinalResponse方法。
handleFinalResponse()函数会将所有响应信息装入sp_response,这是一个ATResponse结构，它的成员包括成功与否(success）以及一个中间结果(p_intermediates)。
handleFinalResponse()在将响应结果保存至sp_response后，设置s_commandcond这一条件变量，此条件变量由at_send_command_full_nolock等待。
at_send_command_full_nolock获得到了完整的响应信息(在sp_response中），便开始进行响应信息的处理，最后由RIL_onRequestComplete将响应数据序列化并通过sendResponse传递至与应用层通信的socket，这一部分与RIL_onUnsolicitedResponse()函数的功能非常相似，请参考对主动上报的解析部分。
#### 2.Android RIL与 WindowsMobile RIL
Android RIL与WindowsMobile RIL在设计思路上都是作为一个radio的抽象，为上层提供电话服务，但在实现方式上两者有着一定的差异，这种差异的产生主要是源自操作系统机制的不同。
Android RIL被实现为HAL,相对于windows mobile中被实现为驱动的方式，Android RIL模块的内聚性更为理想，可维护性也将更强，你也可以把Android Ril 看做一个中间件。Android RIL部分的开发工作，只需要拿到相应的radio文件描述符，就可以进行操作，无需关注radio的I/O驱动实现。
#### 2.1两者在与应用通信上的实现对比
WindowsMobile RIL在实现与应用的通信时提供了RIL Proxy，在这个层面中它定义了大量的RIL_***()函数来作为电话服务请求。这一点与Android RIL的实现比较相似，Android RIL中在ril.h内提供了一系列的宏来定义电话服务请求。
在Android中的rild功能类似于windows mobile RIL的RIL proxy。它同样也是起到一个中介的作用，为上层接口向下传递请求，并上传回响应。在windows mobile RIL中要为每一个应用程序客户提供一份Ril Proxy实例。
对于这两种操作系统平台，RIL所定义的所有请求是不可更改的。
#### 2.2两者在线程结构与回调机制上的对比
在windows mobile的设计中，request与response被设计为异步执行的，他们分别使用两个队列来对它们的异步行为进行管理，执行命令下发和上报命令处理的过程也互不影响，下发命令与命令的相应响应之间的依赖关系由应用程序来捏合。
在android ril中的request与response设计与windows mobile不同，它的命令与响应之间是同步的过程。也就是说一条命令被下发后，将等待执行结果，并进行处理，再上向上层发。而不是直接异步的进行处理和向上发送。
#### 3.Android RIL porting
#### 3.1.命名
要实现某个无线模块的RIL，需要创建一个实现了所有请求方法的共享库，保证Android能够响应无线通信请求。所有的请求被定义ril.h中。
不同的Modem使用不同的端口，这个在init.rc中设置。
Android提供了一个参考Vendor RIL，RIL参考源码在reference-ril。
将你自己的Vendor RIL实现编译为共享库形式：libril-<companyname>-<RIL version>.so
比如：
libril-techfaith-124.so
其中：
libril：所有vendor RIL的开头
<companyname>：公司缩写
<RIL version>：RIL版本number
so：文件扩展
#### 3.2.Android RIL的配置与加载
在init.rc文件中，将通过这种方式来进行Android RIL的加载。
service ril-daemon /system/bin/rild -l /system/lib/libreference-ril.so -- -d /dev/ttyS0
也可以手动加载：
/system/bin/rild -l /system/lib/libreference-ril.so -- -d /dev/ttyS0
这两种方式，都将启动rild守护进程，然后通过-l参数将libreference-ril.so共享库链入，libreference-ril.so的参数-d是指加载一个串口设备，/dev/ttyS0则是这个串口设备的具体设备文件，除了参数-d外，还有-s代表加载类型为socket的设备，-p代表回环接口。
#### 3.3.Android RIL的编译结构
rild:
被编译成可执行文件，rild以守进程的形式执行。
libril:
将被编译为共享库，并被链入rild。
Vendor RIL:
可以以两种方式来运行，如果定义了RIL_SHLIB宏,那么它将被编译成共享库，如果没定义RIL_SHLIB宏，它将以守护进程程序的方式被调用执行。