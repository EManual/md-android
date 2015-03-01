这里列了很多，但是大致可以分为四个主要的类ScanResult wifiConfiguration WifiInfo WifiManager
(1)ScanResult，主要是通过wifi 硬件的扫描来获取一些周边的wifi 热点的信息。
在我们进行wifi 搜索的时候，一般会搜到这些信息，首先是接入点名字、接入点信息的强弱、还有接入点使用的安全模式，是WPA、WPE。
打开这个类，我们可以看到以下几个信息
BSSID 接入点的地址，这里主要是指小范围几个无线设备相连接所获取的地址，比如说两台笔记本通过无线网卡进行连接，双方的无线网卡分配的地址。
SSID 网络的名字，当我们搜索一个网络时，就是靠这个来区分每个不同的网络接入点。
Capabilities 网络接入的性能，这里主要是来判断网络的加密方式等。
Frequency频率，每一个频道交互的MHz数。
Level 等级，主要来判断网络连接的优先数。
这里只提供了一个方法，就是将获得信息编程字符串toString()。
(2)wifiConfiguration 在我们连通一个wifi 接入点的时候，需要获取到的一些信息。大家可以跟我们有线的设备进行对比一下。
这里的数据相对来说比较复杂一下。
六个子类
```  
WifiConfiguration.AuthAlgorthm 用来判断加密方法。
WifiConfiguration.GroupCipher 获取使用GroupCipher 的方法来进行加密。
WifiConfiguration.KeyMgmt 获取使用KeyMgmt 进行。
WifiConfiguration.PairwiseCipher 获取使用WPA 方式的加密。
WifiConfiguration.Protocol 获取使用哪一种协议进行加密。
wifiConfiguration.Status 获取当前网络的状态。
```
对于上述加密感兴趣的朋友，可以在网上搜索相关的内容。
(3)WifiInfo 在我们的wifi 已经连通了以后，可以通过这个类获得一些已经连通的wifi 连接的信息获取当前链接的信息，这里信息就比较简单了，这里简单介绍一下这里的方法:
```  
getBSSID() 获取BSSID
getDetailedStateOf() 获取客户端的连通性
getHiddenSSID() 获得SSID 是否被隐藏
getIpAddress() 获取IP 地址
getLinkSpeed() 获得连接的速度
getMacAddress() 获得Mac 地址
getRssi() 获得802.11n 网络的信号
getSSID() 获得SSID
getSupplicanState() 返回具体客户端状态的信息
```
(4)wifiManager这个不用说，就是用来管理我们的wifi连接，这里已经定义好了一些类，可以供我们使用。这里来说相对复杂，里面的内容比较多，但是通过字面意思，我们还是可以获得很多相关的信息。这个类里面预先定义了许多常量，我们可以直接使用，不用再次创建。
这里还是简单介绍一下这里的方法：
```  
addNetwork(WifiConfiguration config) 通过获取到的网络的链接状态信息，来添加网络
calculateSignalLevel(int rssi , int numLevels) 计算信号的等级
compareSignalLevel(int rssiA, int rssiB) 对比连接A 和连接B
createWifiLock(int lockType, String tag) 创建一个wifi 锁，锁定当前的wifi 连接
disableNetwork(int netId) 让一个网络连接失效
disconnect() 断开连接
enableNetwork(int netId, Boolean disableOthers) 连接一个连接
getConfiguredNetworks() 获取网络连接的状态
getConnectionInfo() 获取当前连接的信息
getDhcpInfo() 获取DHCP 的信息
getScanResulats() 获取扫描测试的结果
getWifiState() 获取一个wifi 接入点是否有效
isWifiEnabled() 判断一个wifi 连接是否有效
pingSupplicant() ping 一个连接，判断是否能连通
ressociate() 即便连接没有准备好，也要连通
reconnect() 如果连接准备好了，连通
removeNetwork() 移除某一个网络
saveConfiguration() 保留一个配置信息
setWifiEnabled() 让一个连接有效
startScan() 开始扫描
updateNetwork(WifiConfiguration config) 更新一个网络连接的信息
```
此外wifiManaer 还提供了一个内部的子类，也就是wifiManagerLock，WifiManagerLock 的作用是这样的，在普通的状态下，如果我们的wifi 的状态处于闲置，那么网络的连通，将会暂时中断。但是如果我们把当前的网络状态锁上，那么wifi 连通将会保持在一定状态，当然接触锁定之后，就会恢复常态。