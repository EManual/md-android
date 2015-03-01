Android Emulator usage:
```  
emulator [options] [-qemu args] 
```
options: 
```  
-sysdir <dir>      			   在目录<dir>中搜索system.img 
-system <file>                 读取system.img文件<file>    
-datadir <dir>                 写入用户数据到目录 <dir> 
-kernel <file>                 使用指定内核kernel-qemu文件 
-ramdisk <file>                指定ram 镜像文件ramdisk.img 
-image <file>                  obsolete, use -system <file> instead 
-init-data <file>              initial data image (default <system>/userdata.img 
-initdata <file>               same as '-init-data <file>' 
-data <file>                   data image (default <datadir>/userdata-qemu.img
-partition-size <size>         分区大小MBs 
-cache <file>                  cache.img 
-no-cache                      禁止缓存 
-nocache                       禁止缓存 
-sdcard <file>                 sdcard.img 
-wipe-data                     reset the use data image (copy it from initdata) 
-avd <name>                    使用指定AVD设备 
-skindir <dir>                 search skins in <dir> (default <system>/skins)  可以自定义
-skin <name>                   select a given skin 
-no-skin                       don't use any emulator skin 
-noskin                        same as -no-skin 
-memory <size>                 物理内存大小MBs 
-netspeed <speed>              最大网速 
-netdelay <delay>              网络延迟 
-netfast                       disable network shaping 
-trace <name>                  enable code profiling (F9 to start) 
-show-kernel                   显示内核消息 
-shell                         终端启用root shell 
-no-jni                        disable JNI checks in the Dalvik runtime 
-nojni                         same as -no-jni 
-logcat <tags>                 查看日志 
-no-audio                      disable audio support 
-noaudio                       same as -no-audio 
-audio <backend>               use specific audio backend 
-audio-in <backend>            use specific audio input backend 
-audio-out <backend>           use specific audio output backend 
-raw-keys                      disable Unicode keyboard reverse-mapping 
-radio <device>                无线猫 
-port <port>                   连接控制台的TCP端口. 
-ports <consoleport>,<adbport> TCP ports used for the console and adb bridge 
-onion <image>                 use overlay PNG image over screen 
-onion-alpha <%age>            specify onion-skin translucency 
-onion-rotation 0|1|2|3        specify onion-skin rotation 
-scale <scale>                 窗口缩放 
-dpi-device <dpi>              specify device's resolution in dpi (default 165) 
-http-proxy <proxy>            HTTP/HTTPS 代理 
-timezone <timezone>           时区 
-dns-server <servers>          DNS服务器 
-cpu-delay <cpudelay>          throttle CPU emulation 
-no-boot-anim                  disable animation for faster boot 
-no-window                     disable graphical window display 
-version                       版本 
-report-console <socket>       report console port to remote socket 
-gps <device>                  redirect NMEA GPS to character device 
-keyset <name>                 specify keyset file name 
-shell-serial <device>         specific character device for root shell 
-old-system                    support old (pre 1.4) system images 
-tcpdump <file>                记录网络数据包 
-bootchart <timeout>           enable bootcharting 
-prop <name>=<value>           设置系统属性 
-qemu args...                 pass arguments to qemu  (比如 -qemu -serial stdio)
-qemu -h                      显示qemu帮助 
-verbose                      same as '-debug-init' 
-debug <tags>                 enable/disable debug messages 
-debug-<tag>                  enable specific debug messages 
-debug-no-<tag>               disable specific debug messages 
-help                         帮助 
-help-<option>                print option-specific help 
-help-disk-images             about disk images 
-help-keys                    supported key bindings 
-help-debug-tags              debug tags for -debug <tags> 
-help-char-devices            character <device> specification 
-help-environment             environment variables 
-help-keyset-file             key bindings configuration file 
-help-virtual-device          virtual device management 
-help-sdk-images              about disk images when using the SDK 
-help-build-images            about disk images when building Android 
-help-all                     帮助(所有) 
emulator -cache cache.img (浏览器暂存到存储空間)
emulator -wipe-data (使模拟器恢复到原来设定)
emulator -no-boot-anim (省略开机小机器人启动动画)
emulator -timezone Asia/Taipei (指定区域)
```