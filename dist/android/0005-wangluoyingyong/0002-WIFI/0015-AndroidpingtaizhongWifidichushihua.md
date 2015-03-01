#### 1. WIFI JAVA layer:
1.1. 当android系统启动WIFI 模块时， 它将调用 Wifiserver 类的setWifiEnabledBlocking函数。
1.2. 在该函数中，如果是使能WIFI, 它将做四件事：
```  
a. load wifi driver.
b. 启动wpa_supplicant.
c. 启动 event loop.
d. 更新wifi的状态.
```
#### 2. WIFI Native layer:
2.1. 当java层调用 loadDriver时, 它实际上是通过JNI来调用Native函数， JNI->android_net_wifi_loadDriver -> wifi_load_driver。
在wifi_load_driver函数中，它将首先通过system property -- wlan.driver.status 的状态来判断驱动是否已经加载。如果没有加载，将会加载该驱动。
2.2. 当java层调用startSupplicant时，它实际上是通过JNI调用到wifi_start_supplicant函数，
在wifi_start_supplicant函数里，首先确定wpa supplicant的配置文件存在，如果不存在，将默认配置文件拷贝到相应目录下，下面是配置文件的默认路径和工作路径：
static const char SUPP_CONFIG_TEMPLATE[]= "/system/etc/wifi/wpa_supplicant.conf";
static const char SUPP_CONFIG_FILE[]= "/data/misc/wifi/wpa_supplicant.conf";
然后，调用control_supplicant函数, 如果这时wpa_supplicant还没有启动, 将会启动wpa_supplicant.
2.3. java层通过connectToSupplicant调用wifi_connect_to_supplicant函数，在该函数中，将通过wpa_ctrl_open函数分别创建两个AF_UNIX地址族和数据报方式的socket，一个是ctrl_conn,用于向wpa_supplicant发送命令并接收response,另一个是monitor_conn,它一直阻塞等待从wpa_supplicant过来的event。最后，通过monitor_conn向wpa_supplicant发送命令ATTACH，用于将自己的socket信息注册到wpa_supplicant,由于socket是数据报方式的，这一步是必须的，对于存在于wpa_supplicant的服务器端，它是所有客户端共享的，由于它需要主动向monitor_conn客户端发送事件，所以它必须先记录下该客户端的详细信息，wpa_supplicant就可以将EVENT发向该socket。
在完成上面这些操作后，java层会通过jni方式调用函数android_net_wifi_waitForEvent（应该是起一个线程，在线程里调用），该函数会调用wifi_wait_for_event，在wifi_wait_for_event函数里，会阻塞接收从wpa_supplicant模块传来的事件，一旦wpa_supplicant模块有事件发，wifi_wait_for_event接收到后，会将包含事件的buf通过函数参数的方式回传到java层，java收到事件后，再继续调用wifi_wait_for_event函数进行阻塞等待接收，从而完成一个循环。
2.4. 以上的流程完成以后，WIFI java layer 调用的WIFI native api 就和wpa_supplicant进程就建立了联系，WIFI java layer就可以向wpa_supplicant发送命令和接收response, 并且wpa_supplicant也可以主动向WIFI java layer发送事件了。