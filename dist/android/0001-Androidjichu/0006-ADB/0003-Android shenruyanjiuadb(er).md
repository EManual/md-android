下面是一个展示devices 命令和输出的例子:
```  
$ adb devicesList of devices attached emulator-5554 deviceemulator-5556 deviceemulator-5558 device
```
如果当前没有模拟器/设备运行，adb则返回 no device。
给特定的模拟器/设备实例发送命令
如果有多个模拟器/设备实例在运行，在发布adb命令时需要指定一个目标实例。 这样做，请使用-s 选项的命令。在使用的-s选项是
```  
adb -s < serialNumber> < command>
```
如上所示，给一个命令指定了目标实例，这个目标实例使用由adb分配的序列号。你可以使用 devices 命令来获得运行着的模拟器/设备实例的序列号
示例如下:
```  
adb -s emulator-5556 install helloWorld.apk
```
注意这点，如果没有指定一个目标模拟器/设备实例就执行 -s 这个命令的话，adb会产生一个错误。
#### 安装软件
你可以使用adb从你的开发电脑上复制一个应用程序，并且将其安装在一个模拟器/设备实例。像这样做，使用install命令。这个install命令要求你必须指定你所要安装的.apk文件的路径:
```  
adb install < path_to_apk>
```
为了获取更多的关于怎样创建一个可以安装在模拟器/设备实例上的.apk文件的信息，可参照Android Asset Packaging Tool (aapt)。
要注意的是，如果你正在使用Eclipse IDE并且已经安装过ADT插件，那么就不需要直接使用adb(或者aapt)去安装模拟器/设备上的应用程序。否则，ADT插件代你全权处理应用程序的打包和安装.
#### 转发端口
可以使用forward命令进行任意端口的转发——一个模拟器/设备实例的某一特定主机端口向另一不同端口的转发请求。下面演示了如何建立从主机端口6100到模拟器/设备端口7100的转发。
```  
adb forward tcp:6100 tcp:7100
```
同样地，可以使用adb来建立命名为抽象的UNIX域套接口，上述过程如下所示:
```  
adb forward tcp:6100 local:logd
```
从模拟器/设备中拷入或拷出文件
可以使用adbpull，push 命令将文件复制到一个模拟器/设备实例的数据文件或是从数据文件中复制。install 命令只将一个.apk文件复制到一个特定的位置，与其不同的是，pull 和 push 命令可令你复制任意的目录和文件到一个模拟器/设备实例的任何位置。
从模拟器或者设备中复制文件或目录，使用(如下命):
```  
adb pull < remote> < local>
```
将文件或目录复制到模拟器或者设备，使用(如下命令)
```  
adb push < local> < remote>
```
在这些命令中， < local> 和< remote> 分别指通向自己的发展机(本地)和模拟器/设备实例(远程)上的目标文件/目录的路径。
#### 下面是一个例子：
```  
adb push foo.txt /sdcard/foo.txt
```
#### Adb命令列表
下列表格列出了adb支持的所有命令,并对它们的意义和使用方法做了说明。
```  
Category Command Description Comments
Options -d 仅仅通过USB接口来管理abd. 如果不只是用USB接口来管理则返回错误。
-e 仅仅通过模拟器实例来管理adb. 如果不是仅仅通过模拟器实例管理则返回错误。
-s < serialNumber> 通过模拟器/设备的允许的命令号码来发送命令来管理adb (比如: "emulator-5556")。如果没有指定号码，则会报错。
General devices 查看所有连接模拟器/设备的设施的清单。
help 查看adb所支持的所有命令。
version 查看adb的版本序列号。
Debug logcat [< option>] [< filter-specs>] 将日志数据输出到屏幕上。
bugreport 查看bug的报告，如dumpsys ,dumpstate ,和logcat 信息。
jdwp 查看指定的设施的可用的JDWP信息。可以用 forward jdwp:< pid> 端口映射信息来连接指定的JDWP进程。例如：
adb forward tcp:8000 jdwp:472
jdb -attach localhost:8000
Data install < path-to-apk> 安装Android为(可以模拟器/设施的数据文件.apk指定完整的路径)。
pull < remote> < local> 将指定的文件从模拟器/设施的拷贝到电脑上。
push < local> < remote> 将指定的文件从电脑上拷贝到模拟器/设备中。
Ports and Networking forward < local> < remote> 用本地指定的端口通过socket方法远程连接模拟器/设施 
```
#### 端口需要描述下列信息:
```  
* tcp:< portnum>
* local:< UNIX domain socket name>
* dev:< character device name>
* jdwp:< pid>
ppp < tty> [parm]... 通过USB运行ppp：
* < tty> — the tty for PPP stream. For
```