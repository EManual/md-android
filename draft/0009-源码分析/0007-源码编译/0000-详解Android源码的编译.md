本文将为大家介绍的是如何设置Android源码的编译环境，包括Linux下的配置。主要基于Android 1.0环境，希望对大家了解Android开发有所帮助。
编译环境：Ubuntu8.10
#### 1、安装一些环境
```  
sudo apt-get install build-essential  
sudo apt-get install make  
sudo apt-get install gcc  
sudo apt-get install g++  
sudo apt-get install libc6-dev  
sudo apt-get install patch  
sudo apt-get install texinfo  
sudo apt-get install libncurses-dev  
sudo apt-get install git-core gnupg  
sudo apt-get install flex bison gperf libsdl-dev libesd0-dev libwxgtk2.6-dev build-essential zip curl  
sudo apt-get install ncurses-dev   
sudo apt-get install zlib1g-dev  
sudo apt-get install valgrind  
sudo apt-get install python2.5 
```
安装java环境
```  
sudo apt-get install sun-java6-jre sun-java6-plugin sun-java6-fonts sun-java6-jdk 
```
注：官方文档说如果用sun-java6-jdk可出问题，得要用sun-java5-jdk。经测试发现，如果仅仅make（make不包括make sdk)，用sun-java6-jdk是没有问题的。而make sdk，就会有问题，严格来说是在make doc出问题，它需要的javadoc版本为1.5。
因此，我们安装完sun-java6-jdk后最好再安装sun-java5-jdk，或者只安装sun-java5-jdk。这里sun-java6-jdk和sun-java5-jdk都安装，并只修改javadoc.1.gz和javadoc。因为只有这两个是make sdk用到的。这样的话，除了javadoc工具是用1.5版本，其它均用1.6版本：
```  
sudo apt-get install sun-java5-jdk
```
修改javadoc的link
```  
cd /etc/alternatives  
sudo rm javadoc.1.gz  
sudo ln -s /usr/lib/jvm/java-1.5.0-sun/man/man1/javadoc.1.gz javadoc.1.gz  
sudo rm javadoc  
sudo ln -s /usr/lib/jvm/java-1.5.0-sun/bin/javadoc javadoc
```
#### 2、设置环境变量
vim ~/.bashrc 
在.bashrc中新增或整合PATH变量，如下
java 程序开发/运行的一些环境变量
```  
JAVA_HOME=/usr/lib/jvm/java-6-sun  
JRE_HOME=${JAVA_HOME}/jre  
export Android_JAVA_HOME=$JAVA_HOME  
export CLASSPATH=.:${JAVA_HOME}/lib:$JRE_HOME/lib:$CLASSPATH  
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin  
export JAVA_HOME;  
export JRE_HOME;  
export CLASSPATH;  
HOME_BIN=~/bin/  
export PATH=${PATH}:${JAVA_PATH}:${JRE_PATH}:${HOME_BIN};  
echo $PATH;
```
最后，同步这些变化：
source ~/.bashrc
#### 3、安装repo（用来更新Android源码)
创建~/bin目录，用来存放repo程序，如下：
```  
$ cd ~  
$ mkdir bin 
```
并加到环境变量PATH中，在第2步中已经加入
下载repo脚本并使其可执行：
```  
$ curl http://Android.git.kernel.org/repo >~/bin/repo  
$ chmod a+x ~/bin/repo
```
#### 4、下载 Android源码并更新之
建议不要用repo来下载（Android源码超过1G，非常慢)，直接在网上下载http://www.Androidin.com/bbs/pub/cupcake.tar.gz。而且解压出来的 cupcake下也有.repo文件夹，可以通过repo sync来更新cupcake代码：
```  
tar -xvf  cupcake.tar.gz 
repo sync（更新很慢，用了3个小时) 
```
#### 5、编译Android源码
并得到~/project/Android/cupcake/out 目录
进入Android源码目录：
make这一过程很久（2个多小时)
#### 6、在模拟器上运行编译好Android
Android SDK的emulator程序在Android-sdk-linux_x86-1.0_r2/tools/下，emulator是需要加载一些image的，默认加载Android-sdk-linux_x86-1.0_r2/tools/lib/images下的kernel-qemu（内核) ramdisk.img  system.img  userdata.img
编译好Android之后，emulator在~/project/Android/cupcake/out/host/linux-x86/bin下，ramdisk.img  system.img  userdata.img则在~/project/Android/cupcake/out/target/product/generic下
cd ~/project/Android/cupcake/out/host/linux-x86/bin
增加环境变量
```  
vim ~/.bashrc 
```
在.bashrc中新增环境变量，如下
java 程序开发/运行的一些环境变量 
```  
export Android_PRODUCT_OUT=~/project/Android/cupcake2/out/target/product/generic  
Android_PRODUCT_OUT_BIN=~/project/Android/cupcake2/out/host/linux-x86/bin  
export PATH=${PATH}:${Android_PRODUCT_OUT_BIN}; 
```
最后，同步这些变化：
```  
source ~/.bashrc  
emulator -image system.img -data userdata.img -ramdisk ramdisk.img 
```
最后进入Android桌面，就说明成功了。
out/host/linux-x86/bin下生成许多有用工具（包括Android SDK/tools的所有工具)，因此，可以把eclipse中Android SDK的路径指定到out/host/linux-x86/bin进行开发
#### 7、编译linux kernel
直接make Android源码时，并没有make linux kernel。因此是在运行模拟器，所以不用编译 linux kernel。如果要移值Android，或增删驱动，则需要编译 linux kernel
#### 8、编译模块
Android中的一个应用程序可以单独编译，编译后要重新生成system.img
在源码目录下执行
```  
.build/envsetup.sh （.后面有空格) 
```
就多出一些命令：
```  
- croot:   Changes directory to the top of the tree.  
- m:       Makes from the top of the tree.  
- mm:      Builds all of the modules in the current directory.  
- mmm:     Builds all of the modules in the supplied directories.  
- cgrep:   Greps on all local C/C++ files.  
- jgrep:   Greps on all local Java files.  
- resgrep: Greps on all local res/*.xml files.  
- godir:   Go to the directory containing a file. 
```
可以加—help查看用法
我们可以使用mmm来编译指定目录的模块，如编译联系人：
```  
mmm packages/apps/Contacts/ 
```
编完之后生成两个文件：
```  
out/target/product/generic/data/app/ContactsTests.apk  
out/target/product/generic/system/app/Contacts.apk
```
可以使用make snod重新生成system.img
再运行模拟器
#### 9、编译SDK
直接执行make是不包括make sdk的。make sdk用来生成SDK，这样，我们就可以用与源码同步的SDK来开发 Android了。
1)修改/frameworks/base/include/utils/Asset.h
‘UNCOMPRESS_DATA_MAX = 1 * 1024 * 1024’ 改为 ‘UNCOMPRESS_DATA_MAX = 2 * 1024 * 1024’
原因是Eclipse编译工程需要大于1.3M的buffer
2)编译ADT。
注意，我们是先执行2)，再执行3)。因为在执行./build_server.sh时，会把生成的SDK清除了。
用上了新的源码，adt这个调试工具也得自己来生成，步骤如下：
进入cupcake源码的development/tools/eclipse/scripts目录，执行：
export ECLIPSE_HOME=你的eclipse路径
./build_server.sh 你想放ADT的路径
3)执行make sdk。
注意，这里需要的javadoc版本为1.5，所以你需要在步骤1中同时安装sun-java5-jdk
make sdk
编译很慢。编译后生成的SDK存放在out/host/linux-x86/sdk/，此目录下有Android-sdk_eng.xxx_linux-x86.zip和Android-sdk_eng.xxx_linux-x86目录。Android-sdk_eng.xxx_linux-x86就是SDK目录
实际上，当用mmm命令编译模块时，一样会把SDK的输出文件清除，因此，最好把Android-sdk_eng.xxx_linux-x86移出来
4)关于环境变量、Android工具的选择
目前的Android工具有：
A、我们从网上下载的SDK（ tools下有许多Android工具，lib/images下有img映像)
B、我们用make sdk编译出来的SDK（ tools下也有许多Android工具，lib/images下有img映像)
C、我们用make编译出来的out目录（ tools下也有许多Android工具，lib/images下有img映像)
那么我们应该用那些工具和img呢？
首先，我们不会用A选项的工具和img，因为一般来说它比较旧，也源码不同步。测试发现，如果使用B选项的工具和img，Android模拟器窗口变小（可能是skin加载不了)，而用C选项的工具和img则不会有此问题。
有些Android工具依赖Android.jar（比如Android)，因此，我们在eclipse中使用B选项的工具（SDK)，使用C选项的img。其实，从emulator -help-build-images也可以看出，Android_PRODUCT_OUT是指向C选项的img目录的
不过，除了用A选项的工具和img，用B或C的模拟器都不能加载sdcard，原因还不清楚。
5)安装、配置ADT
安装、配置ADT请参考官方文档
6)创建Android Virtual Device
编译出来的SDK是没有AVD（Android Virtual Device)的，我们可以通过Android工具查看：
Android list
输出为：
```  
Available Android targets:  
Android 1.5  
API level: 3  
Skins: HVGA-P, QVGA-L, HVGA-L, HVGA (default), QVGA-P  
Available Android Virtual Devices: 
```
表明没有AVD。如果没有AVD，eclipse编译工程时会出错（Failed to find a AVD compatible with target 'Android 1.5'. Launch aborted.)
创建AVD：
```  
Android create avd -t 1 -c ~/sdcard.img -n myavd
```
可以Android –help来查看上面命令选项的用法。创建中有一些选项，默认就行了
再执行Android list，可以看到AVD存放的位置
以后每次运行emulator都要加-avd myavd或@myavd选项，这里eclipse才会在你打开的emulator中调试程序
注意：
这样，SDK和ADT就生成了，就按照官方文档把他们整合到Eclipse，这里不再细说了。
建个Android的新工程，试试你自己编译的sdk。