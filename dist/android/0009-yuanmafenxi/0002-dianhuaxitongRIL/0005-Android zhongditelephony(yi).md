#### 第一部分 c代码
Android源码中，hardware/ril目录中包含着Android的telephony底层源码。这个目录下包含着三个子目录，下面是对三个子目录的具体分析。
####  一、目录hardware/ril/include分析：                                               
只有一个头文件ril.h包含在此目录下。ril.h中定义了76个如下类型的宏：RIL_REQUEST_XXX。
这些宏代表着客户进程可以向Android telephony发送的命令，包括SIM卡相关的功能，打电话，发短信，网络信号查询等。好像没有操作地址本的功能？
#### 二、目录hardware/ril/libril分析。
本目录下代码负责与客户进程进行交互。在接收客户进程命令后，调用相应函数进行处理，然后将命令响应结果传回客户进程。在收到来自网络端的事件后，也传给客户进程。
文件ril_commands.h：列出了telephony可以接收的命令；每个命令对应的处理函数；以及命令响应的处理函数。
文件ril_unsol_commands.h：列出了telephony可以接收的事件类型；对每个事件的处理函数；以及WAKE Type???
文件ril_event.h/cpp：处理与事件源(端口，modem等)相关的功能。ril_event_loop监视所有注册的事件源，当某事件源有数据到来时，相应事件源的回调函数被触发(firePending -> ev->func())
文件ril.cpp：
RIL_register函数：打开监听端口，接收来自客户进程的命令请求 (s_fdListen = android_get_control_socket(SOCKET_NAME_RIL);），当与某客户进程连接建立时，调用listenCallback函数；创建一单独线程监视并处理所有事件源 (通过ril_event_loop)
listenCallback函数：当与客户进程连接建立时，此函数被调用。此函数接着调用processCommandsCallback处理来自客户进程的命令请求。
processCommandsCallback函数：具体处理来自客户进程的命令请求。对每一个命令，ril_commands.h中都规定了对应的命令处理函数(dispatchXXX)，processCommandsCallback会调用这个命令处理函数进行处理。
dispatch系列函数：此函数接收来自客户进程的命令己相应参数，并调用onRequest进行处理。
RIL_onUnsolicitedResponse函数：将来自网络端的事件封装(通过调用responseXXX）后传给客户进程。
RIL_onRequestComplete函数：将命令的最终响应结构封装(通过调用responseXXX）后传给客户进程。
response系列函数：对每一个命令，都规定了一个对应的response函数来处理命令的最终响应；对每一个网络端的事件，也规定了一个对应的response函数来处理此事件。response函数可被onUnsolicitedResponse或者onRequestComplete调用。
#### 三、目录hardware/ril/reference-ril分析。本目录下代码主要负责与modem进行交互。 
文件reference-ril.c：此文件核心是两个函数：onRequest和onUnsolicited
onRequest 函数：在这个函数里，对每一个RIL_REQUEST_XXX请求，都转化成相应的AT command，发送给modem，然后睡眠等待。当收到此AT command的最终响应后，线程被唤醒，将响应传给客户进程(RIL_onRequestComplete -> sendResponse）。
onUnsolicited函数：这个函数处理modem从网络端收到的各种事件，如网络信号变化，拨入的电话，收到短信等。然后将时间传给客户进程 (RIL_onUnsolicitedResponse -> sendResponse）
文件atchannel.c：负责向modem读写数据。其中，写数据(主要是AT command)功能运行在主线程中，读数据功能运行在一个单独的读线程中。
函数at_send_command_full_nolock：运行在主线程里面。将一个AT command命令写入modem后进入睡眠状态(使用pthread_cond_wait或类似函数），直到modem读线程将其唤醒。唤醒后此函数获得了AT command的最终响应并返回。