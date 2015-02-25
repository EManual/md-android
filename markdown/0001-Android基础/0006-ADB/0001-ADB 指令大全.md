#### ADB是什么?
ADB的全称为Android Debug Bridge，就是起到调试桥的作用。
通过adb我们可以在Eclipse中方面通过DDMS来调试Android程序，说白了就是debug工具。adb的工作方式比较特殊，采用监听Socket TCP 5554等端口的方式让IDE和Qemu通讯，默认情况下adb会daemon相关的网络端口，所以当我们运行Eclipse时adb进程就会自动运行。
#### ADB有什么用?
借助ADB工具，我们可以管理设备或手机模拟器的状态。还可以进行很多手机操作，如安装软件、系统升级、运行shell命令等等。其实简而言说，adb就是连接Android手机与PC端的桥梁，可以让用户在电脑上对手机进行全面的操作。
1. 显示系统中全部Android平台：
```  
android list targets
```
2. 显示系统中全部AVD（模拟器）：
```  
android list avd
```
3. 创建AVD（模拟器）：
```  
android create avd --name 名称 --target 平台编号
```
4. 启动模拟器：
```  
emulator -avd 名称 -sdcard ~/名称.img (-skin 1280x800)
```
5. 删除AVD（模拟器）：
```  
android delete avd --name 名称
```
6. 创建SDCard：
```  
mksdcard 1024M ~/名称.img
```
7. AVD(模拟器)所在位置：
```  
Linux(~/.android/avd)      
Windows(C:\Documents and Settings\Administrator\.android\avd)
```
8. 启动DDMS：
```  
ddms
```
9. 显示当前运行的全部模拟器：
```  
adb devices
```
10. 对某一模拟器执行命令：
```  
abd -s 模拟器编号 命令
```
11. 安装应用程序：
```  
adb install -r 应用程序.apk
```
12. 获取模拟器中的文件：
```  
adb pull <remote> <local>
```
13. 向模拟器中写文件：
```  
adb push <local> <remote>
```
14. 进入模拟器的shell模式：
```  
adb shell
```
15. 启动SDK，文档，实例下载管理器：
```  
android
```
16. 卸载apk包：
```  
adb shell
cd data/app
rm apk包
exit
adb uninstall apk包的主包名
adb install -r apk包
```
17. 查看adb命令帮助信息：
```  
adb help
```
18. 在命令行中查看LOG信息：
```  
adb logcat -s 标签名
```
19. adb shell后面跟的命令主要来自：
```  
源码\system\core\toolbox目录和源码\frameworks\base\cmds目录。
```
20. 删除系统应用：
```  
adb remount （重新挂载系统分区，使系统分区重新可写）。
adb shell
cd system/app
rm *.apk
```
21. 获取管理 员权限：
```  
adb root
```
22. 启动Activity：
```  
adb shell am start -n 包名/包名＋类名（-n 类名,-a action,-d date,-m MIME-TYPE,-c category,-e 扩展数据,等）。
```
23、发布端口：
你可以设置任意的端口号，做为主机向模拟器或设备的请求端口。如： 
```  
adb forward tcp:5555 tcp:8000
```
24、复制文件：
你可向一个设备或从一个设备中复制文件，复制一个文件或目录到设备或模拟器上： 
```  
adb push <source> <destination></destination></source> 
如：adb push test.txt /tmp/test.txt 
从设备或模拟器上复制一个文件或目录： 
adb pull <source> <destination></destination></source> 
如：adb pull /addroid/lib/libwebcore.so .
```
25、搜索模拟器/设备的实例：
取得当前运行的模拟器/设备的实例的列表及每个实例的状态： 
```  
adb devices
```
26、查看bug报告： 
```  
adb bugreport 
```
27、记录无线通讯日志：
一般来说，无线通讯的日志非常多，在运行时没必要去记录，但我们还是可以通过命令，设置记录： 
```  
adb shell 
logcat -b radio
```
28、获取设备的ID和序列号：
```  
adb get-product 
adb get-serialno
```
29、访问数据库SQLite3
```  
adb shell 
sqlite3
```
cd system/sd/data //进入系统内指定文件夹 
ls //列表显示当前文件夹内容 
rm -r xxx //删除名字为xxx的文件夹及其里面的所有文件 
rm xxx //删除文件xxx 
rmdir xxx //删除xxx的文件夹
【操作命令】
1. 查看设备
```  
adb devices
```
这个命令是查看当前连接的设备，连接到计算机的android设备或者模拟器将会列出显示。
2.安装软件
```  
adb install
adb install <apk文件路径> :这个命令将指定的apk文件安装到设备上
```
3. 卸载软件
```  
adb uninstall <软件名>
adb uninstall -k <软件名>
```
如果加 -k 参数,为卸载软件但是保留配置和缓存文件.
4. 进入设备或模拟器的shell：
```  
adb shell
```
通过上面的命令，就可以进入设备或模拟器的shell环境中，在这个Linux Shell中，你可以执行各种Linux的命令，另外如果只想执行一条shell命令，可以采用以下的方式：
```  
adb shell [command]
如：adb shell dmesg会打印出内核的调试信息。
```
5. 发布端口
可以设置任意的端口号，做为主机向模拟器或设备的请求端口。如：
```  
adb forward tcp:5555 tcp:8000
```
6. 从电脑上发送文件到设备
```  
adb push <本地路径> <远程路径>
```
用push命令可以把本机电脑上的文件或者文件夹复制到设备(手机)
7. 从设备上下载文件到电脑
```  
adb pull <远程路径> <本地路径>
```
用pull命令可以把设备(手机)上的文件或者文件夹复制到本机电脑
8、查看bug报告
```  
adb bugreport
```
9、记录无线通讯日志
一般来说，无线通讯的日志非常多，在运行时没必要去记录，但我们还是可以通过命令，设置记录：
```  
adb shell
logcat -b radio
```
10、获取设备的ID和序列号
```  
adb get-product
adb get-serialno
adb shell
sqlite3
```