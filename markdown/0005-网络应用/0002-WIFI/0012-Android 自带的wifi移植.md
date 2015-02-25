最近移植wifi到Eclair，Froyo平台。由于没有记录下移植步骤和心得，以至于每次都浪费了大量的精力。在此记录下移植步骤和心得，并和大家分享，如果有错误欢迎指正。
1、在你的BoardConfig.mk文件中增加一行
```  
BOARD_WPA_SUPPLICANT_DRIVER := WEXT
```
2、在你的board配置目录下创建一个wpa_supplicant.conf文件，输入如下内容：
```  
ctrl_interface=DIR=/data/system/wpa_supplicant
update_config=1
```
3、copy网络驱动模块ko文件到你的board配置目录下，下文假设网卡驱动模块为LK_DRV_USB_RTL8192.ko。
4、修改board配置目录下的AndroidBoard.mk，增加如下代码：
```  
file := $(TARGET_OUT)/lib/modules/LK_DRV_USB_RTL8192.ko
ALL_PREBUILT += $(file)
$(file) : $(LOCAL_PATH)/LK_DRV_USB_RTL8192.ko | $(ACP)
$(transform-prebuilt-to-target)
file := $(TARGET_OUT_ETC)/wifi/wpa_supplicant.conf
ALL_PREBUILT += $(file)
$(file) : $(LOCAL_PATH)/wpa_supplicant.conf | $(ACP)
$(transform-prebuilt-to-target)
```
5、修改hardware/libhardware_legacy/wifi/wifi.c文件。
重新定义WIFI_DRIVER_MODULE_PATH和WIFI_DRIVER_MODULE_NAME宏，定义如下：
```  
ifndef WIFI_DRIVER_MODULE_PATH
define WIFI_DRIVER_MODULE_PATH "/system/lib/modules/LK_DRV_USB_RTL8192.ko"
endif
ifndef WIFI_DRIVER_MODULE_NAME
define WIFI_DRIVER_MODULE_NAME "LK_DRV_USB_RTL8192"
endif
```
6、修改init.rc文件，修改如下：
```  
chmod 0771 /system/etc/wifi wifi wifi
chmod 0660 /system/etc/wifi/wpa_supplicant.conf
chown wifi wifi /system/etc/wifi/wpa_supplicant.conf
mkdir /data/misc/wifi 0771 wifi wifi
mkdir /data/misc/wifi/sockets 0771 wifi wifi
 wpa_supplicant socket
mkdir /data/system/ 0771 system system
mkdir /data/system/wpa_supplicant 0771 wifi wifi
mkdir /data/misc/dhcp 0771 system system
setprop wifi.interface wlan0
ice wpa_supplicant /system/bin/wpa_supplicant -dd -Dwext -iwlan0 -c /system/etc/wifi/wpa_supplicant.conf
group system wifi inet
disabled
oneshot
ice dhcpcd /system/bin/dhcpcd wlan0
group system dhcp
disabled
oneshot
```