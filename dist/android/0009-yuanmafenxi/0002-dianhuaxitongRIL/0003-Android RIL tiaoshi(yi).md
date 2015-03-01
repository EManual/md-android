#### 1. 所要了解的一些源码及脚本文件：
```  
Android/hardware/ril/reference_ril/ (reference_ril.c)
Android/hardware/ril/rild
Android/extern/ppp/pppd
Android/extern/ppp/chat
Android/data/etc/apn-conf-sdk.xml
Android/system/core/rootdir/etc/ppp/init.gprs-pppd
Android/system/core/rootdir/etc/ppp/peers/cmnet
Android/system/core/rootdir/etc/ppp/chat/cmtc-isp
Android/vendor/xxxxx/xxxx/system.prop
```
reference_ril.c：RIL的一些AT命令操作，通过一些onRequest接口操作，对不同的硬件，需作一些修改调整。
apn-conf-sdk.xml：以下是一个例子，有些不支持的APN，需要自己加上去，否则在log 中会出现类似：No APN found for carrier： 46xxx， 的错误.一般移动的TD USIM是46007，有些是46000。
```  
<apns version="6">
	<apn carrier="Android"
		mcc="310"
		mnc="995"
		apn="internet"
		user="*"
		server="*"
		password="*"
		mmsc="null"
		/>
	<apn carrier="TelKila"
		mcc="310"
		mnc="260"
		apn="internet"
		user="*"
		server="*"
		password="*"
		mmsc="null"
		/>
	<apn carrier="CMCC"
		mcc="460"
		mnc="00"
		apn="cmnet"
		user="*"
		server="*"
		password="*"
		mmsc="null"
		/>
	<apn carrier="CHINA MOBILE"
		mcc="460"
		mnc="07"
		apn="cmnet"
		user="*"
		server="*"
		password="*"
		mmsc="null"/>
</apns>
```
init.gprs-pppd: 调用pppd GPRS拨号的初始化脚本。
```  
PPPD_PID=
/system/bin/setprop "net.gprs.ppp-exit" 
/system/bin/log -t pppd "Starting pppd"
/system/xbin/pppd call cmnet $*
PPPD_EXIT=$?
PPPD_PID=$!
/system/bin/log -t pppd "pppd exited with $PPPD_EXIT"
/system/bin/setprop "net.gprs.ppp-exit" "$PPPD_EXIT"
```
cmnet：pppd拨号option脚本：
```  
/dev/ttyACM2
921600
nocrtscts
nocdtrcts
local
usepeerdns
defaultroute
noipdefault
ipcp-accept-local
ipcp-accept-remote
user cmnet
password cmnet
lock
nodetach
connect "/system/xbin/chat -v -t 50 -f /system/etc/ppp/chat/cmtc-isp
cmtc-isp：
ABORT 'BUSY
ABORT 'NO CARRIER
ABORT 'ERROR
ABORT '+CME ERROR: 100
"" AT
OK AT+CGDCONT=1,"IP","CMNET"
OK AT+CGEQREQ=1,2,128,384,0,0,0,0,"0E0","0E0",,0,0
OK AT
OK AT
OK ATS0=0
OK AT
OK AT
OK ATDT*98*1
CONNECT
system.prop
rild.libpath=/system/lib/libreference-ril.so
rild.libargs=-d /dev/ttyACM0	
```