要玩GPhone的模拟器，当然需要先去google上面下载Android的SDK，解压出来后在SDK的根目录下有一个tools文件夹，里面就是模拟器和一些非常有用的工具。
双击“emulator.exe”，直接启动模拟器，简单吧。当然，如果要对模拟器进行一些定制，还是要从命令行调用，带上参数启动。下面就来介绍一下启动是常用的几个参数：
#### 1、模拟器外观的定制：
480x320, landscape: emulator -skin HVGA-L
320x480, portrait : emulator -skin HVGA-P (default)
320x240, landscape: emulator -skin QVGA-L
240x320, portrait : emulator -skin QVGA-P
#### 2、模拟器上SD卡的使用：
(1) 首先是生成sdcard镜像文件sdcard.img或者是其他的名称。
```  
命令为：mksdcard -l sdcard capacity directory
例如：mksdcard 1024M D:\sdcard.img
```
directory 指的是镜像文件存放的目录，capacity就是要创建的镜像文件的容量。这里将镜像文件放在当前目录下。
(2) 之后，启动模拟器：emulator -sdcard sdcard镜像文件的目录
```  
例如：emulator -sdcard D:\sdcard.img
```
(3) 打开另外一个终端，输入adb push 命令来将资源放入到sdcard里面
```  
例如：adb push D:/audio.mp3 sdcard
```
我们检查一下是否将文件放入了sdcard中，使用命令adb shell , ls , cd sdcard，ls，发现存在aduio.mp3文件，即我们的push命令运行成功。
玩过手机模拟器的人一般最感兴趣的当然是模拟器能做什么呢？下面一一道来：
GPhone的模拟器有个特有的号码：15555218135，这个就类似我们实体手机的SIM卡号码啦。要实现拨号，用手机？当然不行！
更简单，三步：
```  
1.运行 cmd
2.连接: telnet localhost 5554
3.命令:gsm call 15555218135
```
look！是不是模拟器上显示来电了？接听/挂断和实体手机一样。
发短信也一样简单，重复上面1，2两步，第三部命令改一下：
```  
sms send 15555218135 Hello,this is a Message.
```
来说说PC与模拟器文件传输的方法吧。这里需要用到另一个重要工具，也在“tools”目录下，“adb.exe”。
#### adb:
adb(Android Debug Bridge)是Android 提供的一个通用的调试工具，借助这个工具，我们可以管理设备或手机 模拟器 的状态 。还可以进行以下的操作：
```  
1、快速更新设备或手机模拟器中的代码，如应用或Android系统升级；
2、在设备上运行shell命令；
3、管理设备或手机模拟器上的预定端口；
4、在设备或手机模拟器上复制或粘贴文件
```
#### 一些常用的操作：
进入Shell: adb shell
通过上面的命令，就可以进入设备或模拟器的shell环境中，在这个Linux Shell中，你可以执行各种Linux 的命令，另外如果只想执行一条shell命令，可以采用以下的方式：
```  
adb shell [command]
```
如：adb shell dmesg会打印出内核的调试信息。
(Android的linux shell做了大量精简，很多linux常用指令都不支持)
上传文件: adb push <PC文件> </tmp/...>
下载文件: adb pull </tmp/...> <PC文件>
安装程序: adb install <*.apk>
卸载软件: adb shell rm /data/app/<*.apk>
补充一点，通过adb安装的软件(*.apk)都在"/data/app/"目录下，所以安装时不必制定路径，卸载只需要简单的执行"rm"就行。
结束adb: adb kill-server
#### 显示android模拟器状态:
```  
adb devices (端口信息)
adb get-product (设备型号)
adb get-serialno (序列号)
等待正在运行的设备: adb wait-for-device
端口转发: adb forward adb forward tcp:5555 tcp:1234
(将默认端口TCP 5555转发到1234端口上)
查看bug报告: adb bugreport
adb shell sqlite3 访问数据库SQLite3
adb shell logcat -b radio 记录无线通讯日志
``` 
一般来说，无线通讯的日志非常多，在运行时没必要去记录，但我们还是可以通过命令，设置记录：
应用程序配置文件:
"AndroidManifest.xml"中
```  
<category android:name="android.intent.category.LAUNCHER" />
```
决定是否应用程序是否显示在Panel上
am指令(在shell内使用am来加载android应用):
```  
am [start|instrument]   
am start [-a <ACTION>]
   [-d <DATA_URI>]
   [-t <MIME_TYPE>]                
   [-c <CATEGORY> [-c <CATEGORY>] ...]
   [-e <EXTRA_KEY> <EXTRA_VALUE> [-e <EXTRA_KEY> <EXTRA_VALUE> ...]
   [-n <COMPONENT>] [-D] [<URI>]       
am instrument [-e <ARG_NAME> <ARG_VALUE>]
   [-p <PROF_FILE>]                
   [-w] <COMPONENT>
```
启动浏览器:
```  
am start -a android.intent.action.VIEW -d http://www.google.cn/
```
拨打电话:
```  
am start -a android.intent.action.CALL -d tel:10086
```
启动google map直接定位到北京:
```  
am start -a android.intent.action.VIEW geo:0,0?q=beijing
```
目录：
```  
 ls
ls
sqlite_stmt_jou
cache
sdcard
etc
init
init.goldfish.r
init.rc
data
system
proc
sys
sbin
default.prop
root
dev
```
这里要说明下，从andorid中得到的文件流的字符串的顺序是按“类型+权限+拥有者+数组+大小+日期+名称+链接到”顺序排列的，其中类型“d”表示的是文件夹，"l"表示的是链接,'-'表示的是文件。
```  
例如d rwxrwx--- system   cache                2009-01-09 11:46              cache
```
上面的目录就是通过解析ls命令返回的字符串进行解析的。
#### 数据库:
```  
联络人(含通话记录)数据库：/data/data/com.android.providers.contacts/databases/contacts.db
媒体库(貌似记录铃声设置等信息): /data/data/com.android.providers.media/internal.db
系统设置: /data/data/com.android.providers.settings/databases/settings.db
短信库: /data/data/com.android.providers.telephony/databases/mmssms.db
Web设置: /data.data/com.android.settings/databases/webview.db
地图搜索历史记录:/data/data/com.google.android.apps.maps/databases/search_history.db
帐号库(内含androidId信息) : /data/data/com.google.android.googleapps/databases/accounts.db
铃声: /system/media/audio
时区设置: /data/property/persist.sys.timezone
```
#### 目前的安装模式
安装前：
```  
1. emulator -wipe-data
2. adb push busybox ./
3. adb shell ./busybox tar -cf /tmp/data.tar /data
4. adb pull /tmp/data.tar .
5. mkdir original
6. cd original
7. tar -xf ../data.tar
```
安装后：
```  
1. adb shell ./busybox tar -cf /tmp/data.tar /data
2. adb pull /tmp/data.tar .
3. mkdir after_install
4. cd after_install
5. tar -xf ../data.tar
```
目前来看，就是/data/app和data/data下多了两个相关文件，同时在/data/system/packages.xml中增加了安装的程序信息。似乎菜单也是从这个文件中得到是否新安装程序，以及如何显示相关信息比如名称什么的。
#### android模拟器和真机的不同之处：
```  
* 不支持呼叫和接听实际来电；但可以通过控制台模拟电话呼叫(呼入和呼出)
* 不支持USB连接
* 不支持相机/视频捕捉
* 不支持音频输入(捕捉)；但支持输出(重放)
* 不支持扩展耳机
* 不能确定连接状态
* 不能确定电池电量水平和交流充电状态
* 不能确定SD卡的插入/弹出
* 不支持蓝牙
```
#### andoroid模拟器使用注意：
平时使用emulator测试开发的网友注意应该定期清理下C:\Documents and Settings\sh\Local Settings\Temp\AndroidEmulator文件夹，由于Android模拟器每次运行时会临时生成几个.tmp后缀的临时文件，没有几个月功夫简单一看竟然占用磁盘空间高达5GB之多。这些文件网友可以安全的删除。