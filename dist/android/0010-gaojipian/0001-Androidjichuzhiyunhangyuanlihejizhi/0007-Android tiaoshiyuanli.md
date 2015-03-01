#### 一、adb的介绍: 
adb(Android Debug Bridge)包括三个部分：
1)adb client, 运行在PC上(为DDMS，即IDE工作)。
2)adb daemon(守护进程), 运行于Emulator(为与Emulator中的VM交互工作)。
3)adb server(服务进程), 运行在PC(任务管理器上有)，管理着adb client和adb daemon的通信.server与client通信的端口是是5037。
adb server会与emulator交互的，使用的端口有两个，一个是5554专门用于与Emulator实例的连接，那么数据可以从Emulator转发给IDE控制台了，另一个则是5555，专门与adb daemon连接为后面调试使用。
PS:Emulator/Device占用两个(一组)端口，一个为偶数的5554，一个奇数的5555。
如果还开启其他的Emulator，则使用的另一组端口是5556，5557，一直到5585。
adb server开启时就是通过查找5555-5585之间端口来建立与模拟器的连接的，建立连接后就可以用adb的相关命令了。
如果您安装了ADT就基本不需要adb的命令了(因为DDMS会调用ADB进行透明操作)。
关于本机的端口使用情况可以使用netstat [-a] [-n]来查询验证一下。
#### 二、观察一组数据：
在开启仿真器时有一些打印：
```  
[2009-06-06 14:04:16 - Helloworld] Android Launch!
[2009-06-06 14:04:17 - Helloworld] adb is running normally.
[2009-06-06 14:04:17 - Helloworld] Performing com.android.hello.Helloworld activity launch
[2009-06-06 14:04:17 - Helloworld] Automatic Target Mode: Preferred AVD 'lab' is not available. Launching new emulator.
[2009-06-06 14:04:17 - Helloworld] Launching a new emulator with Virtual Device 'lab'
[2009-06-06 14:04:24 - Helloworld] New emulator found: emulator-5554
[2009-06-06 14:04:24 - Helloworld] Waiting for HOME ('android.process.acore') to be launched...
[2009-06-06 14:05:45 - Helloworld] HOME is up on device 'emulator-5554'
[2009-06-06 14:05:45 - Helloworld] Uploading Helloworld.apk onto device 'emulator-5554'
[2009-06-06 14:05:45 - Helloworld] Installing Helloworld.apk...
```
每一行都基本表示一个命令在执行，emulator-5554是仿真器的初始端口了。
最后一句等于命令：adb -s emulator-5554 install helloworld.apk
如果报了类似以下的错误，那得(加个-r)重装，因为该App已经在该Emulator下运行了。
```  
DDM dispatch reg wait timeout
Can't dispatch DDM chunk 52454151: no handler defined
Can't dispatch DDM chunk 48454c4f: no handler defined
```
网上没有看到这个错误因此顺便提下解决方法：
```  
adb -s emulator-5554 install -r helloworld.apk
```
#### 三,了解下DDMS:(都是adb的命令相当的功能)
DDMS有几个界面：
1)Devices：可以查看到当前运行的Emulator和其内运行的应用。
2)Emulator control，即仿真器的硬件设置项等：
设置当前注册的网络状态(Home,Roaming,UnRegistered,Searching)
数据业务的速度设置：有GSM,GPRS,EDGE,UMTS,HSDPA(3.5G?)
还有载入KML或NMEA文件来模拟GPS数据。
3)还可以查询Threads,Heap,File Explorer、重启adb,抓屏等，其他都是在调用adb。
4)关于Logcat
从Windows->Prereference->android->DDMS->Loggin Level进行设置打印等级，
不过默认下只打印入口线程的信息，射频和Tapi的动作信息要通过adb Logcat -b radio打开。
Log默认被写入到手机的/data/anr/traces.txt文件中。
#### 四，Debug面板
这个面板对于熟悉Eclipse的用户来说应该不用看了。
通过以下三步将自己的应用或将已经跑起来的应用加入调试列表：
1)选择Devices列表中Your app。
2)选择臭虫按钮将该程序加载进调试状态。
3)OK,加断点吧。不过源代码要最新的否则断点不起作用。
#### 五、DDMS如何让IDE的调试工作起来呢?
1)有几个组成:
一个是adb(Android Debug Bridge)参考第一部分，它起到调试桥的作用。
另一类是运行在Device/Emulator端的adb daemon, VM, debugger, your Applicatioin。
通过下面句话就可以理解它们的关系：
一个App跑在一个进程中，这个进程又被一个VM绑定，都是一对一的，但VM与Emulator显然是多对一的。
那调试时debugger从VM中拿到栈线程进程等信息，而daemon的作用仅仅是被DDMS用于建立一条连接(看下面)。
最后一类则是运行在PC上的DDMS debugger。
这个debugger是IDE的调试器，你可以改成另一个调试器。
DDMS是Dalvik Debug Monitor Service，负责建立调试的作用，它仅有两个Service，其他的功能都是通过ADB client.让IDE与Emulator交互起来的。
2)开启IDE时，DDMS会建立一个Device monitoring service用于监控Emulator,因为可以开启多个Emulator嘛。
如果找到一个Emulator，那么DDMS才会再开启另一个Service叫VM Monitoring Sevice用于监控该Emulator下的VM; 
第一部分提到adb有三个部分，其中的adb client可以多个实例的，DDMS的Service通过从ADB Client与ADb server的交互结果来维护自身的数据。
如果VM Monitor找到Emulator的一个VM，那么DDMS会利用ADB获取目标VM的进程ID。
同时通过client与daemon建立起与vm的debugger的新连接，注意新连接的交互端口是从8600开始的(n个的话端口是8659+n),这条新连接可以让DDMS获得与VM的实际交互。
剩下的就是DDMS把拿到的数据再扔给ide的debugger(它们之间默认通过8700端口，可更改，因为与VM的交互端口从8600开始使用的话可能会不够的)。
这样IDE的Debug视图就能正确工作了。