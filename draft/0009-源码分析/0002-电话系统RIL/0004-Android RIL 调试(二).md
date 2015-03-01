#### 2. 所要具备的调试工具：
microcom:可在linux下通过发送AT命令调试硬件模块，在较新版本busybox中可以找到此模块。如命令：
./microcom -t 12000 /dev/ttyACM0
注： -t 12000 为延迟退出ms时间，不宜太长时间，时间太长，会感觉 像死机，时间太短，经常会命令没输完就退出了。
ppp(pppd， chat）：可手拨号连接GPRS，如命令：
 pppd call cmnet &
#### 3. 设备文件
1）/dev/ttyACM0， /dev/ttyACM1， /dev/ttyACM2
这是usb transceiver 模拟串口的设备文件，如果是直接串口连接，可能是/dev/ttyS0，/dev/mux0等。
2）ifconfig ppp0
GPRS连接上后，会出现ppp0的网络接口，用如上命令可以查看ppp0网络接口属性。
#### 4. 常用AT命令，具体可以查
```  
AT+CFUN
AT+CREG
AT+CGREG
AT
AT+COPS
AT+CPIN
AT+CIMI
AT^SYSCONFIG
ATD
AT+CGDCONT
AT+CGEQREQ
AT+CSQ
```
#### 5. RIL启动过程分析
如果硬件模块驱动配置成功，在/dev目录下会出现类似于ttyACM0，ttyS0等的设备文件。
1-36行： 初始化过程，设备文件由system.prop属性文件决定。
40行： D/RILJ (110): [0000]> SCREEN_STATE 这里开始了第一个onRequest,
然后会开始一系列的onRequest：
```  
40 D/RILJ (110): [0000]> SCREEN_STATE
49 D/RILJ (110): [0001]> RADIO_POWER
62 D/RILJ (110): [0002]> BASEBAND_VERSION
63 D/RILJ (110): [0003]> GET_IMEI
64 D/RILJ (110): [0004]> GET_IMEISV
65 D/RILJ (110): [0005]> REQUEST_GET_ACCESS_MODE
83 D/RILJ (110): [0006]> OPERATOR
84 D/RILJ (110): [0007]> GPRS_REGISTRATION_STATE
85 D/RILJ (110): [0008]> REGISTRATION_STATE
86 D/RILJ (110): [0009]> QUERY_NETWORK_SELECTION_MODE
87 D/RILJ (110): [0010]> GET_CURRENT_CALLS
88 D/RILJ (110): [0011]> OPERATOR
91 D/RILJ (110): [0012]> GPRS_REGISTRATION_STATE
96 D/RILJ (110): [0013]> REGISTRATION_STATE
97 D/RILJ (110): [0014]> QUERY_NETWORK_SELECTION_MODE
100 D/RILJ (110): [0015]> OPERATOR
101 D/RILJ (110): [0016]> GPRS_REGISTRATION_STATE
102 D/RILJ (110): [0017]> REGISTRATION_STATE
103 D/RILJ (110): [0018]> QUERY_NETWORK_SELECTION_MODE
104 D/RILJ (110): [0019]> SET_NETWORK_SELECTION_AUTOMATIC
105 D/RILJ (110): [0020]> OPERATOR
106 D/RILJ (110): [0021]> GPRS_REGISTRATION_STATE
107 D/RILJ (110): [0022]> REGISTRATION_STATE
108 D/RILJ (110): [0023]> QUERY_NETWORK_SELECTION_MODE
116 D/RILJ (110): [0024]> getIMSI:RIL_REQUEST_GET_IMSI 11 GET_IMSI
117 D/RILJ (110): [0025]> GET_SIM_STATUS
118 D/RILJ (110): [0026]> QUERY_FACILITY_LOCK
119 D/RILJ (110): [0027]> QUERY_FACILITY_LOCK
239 D/RILJ (110): [0028]> OPERATOR
240 D/RILJ (110): [0029]> GPRS_REGISTRATION_STATE
241 D/RILJ (110): [0030]> REGISTRATION_STATE
242 D/RILJ (110): [0031]> QUERY_NETWORK_SELECTION_MODE
248 D/RILJ (110): [0032]> OPERATOR
249 D/RILJ (110): [0033]> GPRS_REGISTRATION_STATE
250 D/RILJ (110): [0034]> REGISTRATION_STATE
251 D/RILJ (110): [0035]> QUERY_NETWORK_SELECTION_MODE	
```
以上onRequest不一定都是必须的，如果GPRS_REGISTRATION_STATE，REGISTRATION_STATE出现的注册状态为：
```  
AT< +CGREG：1，1
AT< +CREG：2，1，"a834"，"5692"
```
那么你的恭喜你网络注册成功了，如果出现
```  
AT< +CGREG：1，0
AT< +CREG：2，0
```
那么要查一下为什么注册不成功，检查一下SIM插好没，我发现AT+CFUN=4命令有可能会影响到后来的网络注册。
如果网络没有注册，不会出现以下连接信息
```  
295   D/GSM (110）：[DataConnectionTracker] ***trySetupData due to gprsAttached
```
如果出现此条信息，那么恭喜你又进了一步，要开始下一步ppp拨号上网了，
通过 logcat &命令，会看到
```  
I/pppd (140)：Starting pppd
```
如果拨号成功，会出现如下信息：
```  
Serial connection established.
Using interface ppp0
Connect： ppp0 <--> /dev/ttyACM2
Remote message： Login OK
PAP authentication succeeded
local  IP address 10.77.154.38
remote IP address 192.200.1.21
primary   DNS address 211.136.112.50
secondary DNS address 211.136.20.203
```
这时候网络连接成功，radio log会出现如下信息，
```  
327   D/GSM  (110)：[DataConnectionTracker] setState：CONNECTED
```
这时候就可以放松一下了，郁闷期已过，剩下的稳定性的问题可以慢慢调试了。
```  
PING www.l.google.com (64.233.189.99） 56(84） bytes of data.
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=1 ttl=242 time=196 ms
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=2 ttl=242 time=189 ms
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=3 ttl=242 time=195 ms
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=4 ttl=242 time=199 ms
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=5 ttl=242 time=203 ms
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=6 ttl=242 time=196 ms
64 bytes from hkg01s01-in-f99.1e100.net (64.233.189.99）：icmp_seq=7 ttl=242 time=187 ms
ifconfig ppp0
```