需要提醒你的不能自动启动PDP连接。
Scripting get-serialno 查看adb实例的序列号。
get-state 查看模拟器/设施的当前状态。
wait-for-device 如果设备不联机就不让执行,--也就是实例状态是 device 时. 你可以提前把命令转载在adb的命令器中,在命令器中的命令在模拟器/设备连接之前是不会执行其它命令的。
示例如下:
```  
adb wait-for-device shell getprop
```
需要提醒的是这些命令在所有的系统启动启动起来之前是不会启动adb的 所以在所有的系统启动起来之前你也不能执行其它的命令。
比如：运用install 的时候就需要Android包，这些包只有系统完全启动。
例如：
```  
adb wait-for-device install < app>.apk
```
上面的命令只有连接上了模拟器/设备连接上了adb服务才会被执行，而在Android系统完全启动前执行就会有错误发生。
Server start-server 选择服务是否启动adb服务进程。
kill-server 终止adb服务进程。
Shell shell 通过远程shell命令来控制模拟器/设备实例。 
shell [< shellCommand>] 连接模拟器/设施执行shell命令，执行完毕后退出远程shell端l。
#### 启动shell命令
Adb 提供了shell端，通过shell端你可以在模拟器或设备上运行各种命令。这些命令以2进制的形式保存在本地的模拟器或设备的文件系统中:
/system/bin/...
不管你是否完全进入到模拟器/设备的adb远程shell端，你都能 shell 命令来执行命令。
当没有完全进入到远程shell的时候，这样使用shell 命令来执行一条命令:
adb [-d|-e|-s {< serialNumber>}] shell < shellCommand>
在模拟器/设备中不用远程shell端时，这样使用shell 命 :
adb [-d|-e|-s {< serialNumber>}] shell
通过操作CTRL+D 或exit 就可以退出shell远程连接。
下面一些就将告诉你更多的关于shell命令的知识。
通过远程shell端运行sqllite3连接数据库。
通过adb远程shell端，你可以通过Android软sqlite3命令程序来管理数据库。sqlite3 工具包含了许多使用命令，比如：.dump 显示表的内容，.schema 可以显示出已经存在的表空间的SQL CREATE结果集。Sqlite3还允许你远程执行sql命令。
通过sqlite3，按照前几节的方法登陆模拟器的远程shell端，然后启动工具就可以使用sqlite3命令。当sqlite3启动以后，你还可以指定你想查看的数据库的完整路径。模拟器/设备实例会在文件夹中保存SQLite3数据库。 /data/data/< package_name>/databases/ 。
示例如下:
```  
$ adb -s emulator-5554 shell sqlite3 /data/data/com.example.google.rss.rssexample/databases/rssitems.dbSQLite version 3.3.12Enter ".help" for instructions.... enter commands, then quit...sqlite> .exit
```
当你启动sqlite3的时候，你就可以通过shell端发送 sqlite3 ,命令了。用exit 或 CTRL+D 退出adb远程shell端。
#### UI/软件 试验程序 Monkey
当Monkey程序在模拟器或设备运行的时候，如果用户出发了比如点击，触摸，手势或一些系统级别的事件的时候，它就会产生随机脉冲，所以可以用Monkey用随机重复的方法去负荷测试你开发的软件。
最简单的方法就是用用下面的命令来使用Monkey，这个命令将会启动你的软件并且触发500个事件。
```  
$ adb shell monkey -v -p your.package.name 500
```
#### 其它的shell命令
下面的表格列出了一些adbshell命令，如果需要全部的命令和程序，可以启动模拟器实例并且用adb -help 命令。
adb shell ls /system/bin
对大部分命令来说，help都是可用的。
```  
Shell Command Description Comments
```
dumpsys 清除屏幕中的系统数据n。 
Dalvik Debug Monitor Service (DDMS)工具提供了完整的调试。
dumpstate 清除一个文件的状态。
logcat [< option>]... [< filter-spec>]... 启动信息日志并且但因输出到屏幕上。
dmesg 输出主要的调试信息到屏幕上。
start 启动或重启一个模拟器/设备实例。
stop 关闭一个模拟器/设备实例。