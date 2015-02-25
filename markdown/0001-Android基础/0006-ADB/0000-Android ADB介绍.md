在Android的应用程序开发中，通常使用的是Java语言，除了要熟悉Java语言的基础外，还需要了解Android提供的Java扩展功能。
#### 一、重要包描述
```  
Android.app：提供高层的程序模型、提供基本的运行环境。
Android.content：包含对各种的设备上的数据进行访问和发布的类。
Android.database：通过内容提供者浏览和操作数据库。
Android.graphics：底层的图形库，包含画布、颜色过滤、点、矩形，可以将它们直接绘制到屏幕上。
Android.location：定位和服务的相关类。
Android.media：提供了一些管理音频视频的媒体接口的相关类。
Android.net提供了关于网络访问的类，超过通常的java.net.*接口。
Android.os：提供了系统服务，消息传输，IPC机制。
Android.opengl：提供了OpenGL的工具。
Android.provider：提供类访问Android的内容提供者。
Android.telephony：提供与拨打电话相关的API交互。
Android.view：提供基本的用户界面接口框架。
Android.util：涉及工具性的方法，例如时间日期型的操作。
Android.webkit：默认浏览器操作接口。
Android.widget：包含各种U元素，在应用程序的屏幕中使用。
```
#### 二、Android的相关文件类型概述
#### Java文件---应用程序源文件
Android的应用必须使用Java来开发。
#### Class文件---Java编译后的目标文件。
不想J2SE，java编译成class文件就直接可以运行，Android平台上的class文件不能直接在Android平台上运行.由于google使用了自己的Dalvik来运行应用，所以这里的class也肯定不能在Android Dalvik上运行，Android的class文件实际上只是编译过程的中间目标文件，需要链接成Dex文件才能运行在Dalvik上。
#### Dex文件---Android平台上的可执行文件。
Dalvik执行的并非是Java字节码，而是另一种字节码：dex格式的字节码（Java字节码->dex字节码）.Dalvik可以执行许多VM而不会占用太多的Resource。
#### APK 文件---Android上的安装文件
APK是Android安装包的扩展名，一个Android安装包包含了与某个应用程序相关的所有文件，APK文件将AndroidMainfest.xml文件、应用程序代码（DEX）文件、资源文件和其他文件打成一个压缩包。一个工程只能打进一个.apk文件。
#### 三、Android ADB工具的使用
ADB是Android提供的一个通用调试工具，借助这个工具，我们管理手机模拟器的状态.
#### 1、ADB功能操作
快速更新设备或手机模拟器的代码，如应用或Android系统升级.
在设备上运行shell命令
管理设备或手机模拟器上的预定接口
在设备或手机模拟器上复制、粘贴文件
#### 2、ADB的常用操作
#### 安装应用到模拟器
```  
adb install app.apk
```
Android没有提供一个卸载应用的命令，只能手动删除：
```  
Adb shell
Cd data/app
Rm.app.apk
```
进入设备或模拟器的shell
```  
Adb shell
```
通过以上命令，可以进入设备或模拟器的shell环境中，在这个shell中，你可以执行各种Linux的命令，另外如果只想执行一条shell命令，可以采用以下方式：
```  
Adb shell[command]
如：
Adb shell emesg
```
会打印出内核的调试信息
#### 发布端口
可以设置任意的端口号，作为主机箱模拟器或设备的请求端口.如：
```  
Adb forward tcp ：5555 tcp：8000
```
#### 复制文件
复制一个文件或目录到设备或模拟器上；
```  
Adb push
如：
Adb push test.txt/tmp/test.txt
Adb pull
如：
Adb pull /Android/lib/libwebcore.os
```
#### 搜索/等待模拟器、设备实例
取得当前运行的模拟器、设备的实例列表及每个实例的状态或等待正在运行的设备
```  
Adb devices
Adb wait-for-device
```
#### 查看debug报告
```  
Adb bugreport
```
#### 记录无线通信日志
无线通信日志非常多，在运行时没必要记录，可以通过命令设置记录
```  
Adb shell
Logcat -b radio
```
#### 获取设备ID和序列号
```  
Adb get-product
Adb get-serialno
```