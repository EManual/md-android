#### 1. Windows下能编译Android源代码吗？
目前Android开发网正式Cygwin还无法在Windows下编译Android源代码，不过在Linux或Mac OS这些*nix系统下可以编译。
#### 2. 编译Android源码的JDK版本问题
按照Google官方文档显示编译推荐在JDK 1.5来生成2.2或以前版本系统的ROM，这里推荐大家使用64位的Linux系统来编译Android源代码可以减少很多不必要的错误。同时从Android 2.3姜饼开始使用JDK6来编译源码，这点大家注意。
#### 3. 真的想在Windows下编译源码怎么办？
你可以在Windows下安装虚拟机，这里推荐性能和稳定性较好的VMWare 7.x版本，安装完后不要忘记安装VMWare Tools.这里推荐虚拟机的配置为1.5GB的内存和至少10GB的剩余空间，这里都是Google官方的资料，当然你的PC RAM不是很大可以适当降低，不过会大大增加编译的时间。
#### 4. 如何下载Android源码及配置编译环境
这里我们可以通过手动在/etc/apt/sources.list添加你的系统源，这里以ubuntu为例，修改需要root权限，当然Android123推荐直接使用命令行添加
```  
$sudo add-apt-repository "deb http：//archive.ubuntu.com/ubuntu dapper main multiverse"
$sudo add-apt-repository "deb http：//archive.ubuntu.com/ubuntu dapper-updates main multiverse"
```
接下来需要更新源，执行下面的命令
```  
$sudo apt-get update
```
接下来安装JDK5
```  
$sudo apt-get install sun-java5-jdk
```
然后配置JDK5为默认的Java开发环境
```  
$sudo update-java-alternatives -s java-1.5.0-sun
```
接下来下载安装相关的库文件，比如python、g++、git、zlib、curl等等，部分版本可能上面的这个源不存在，可以添加一些国内的源，经过Android123证实哈工大的源run.hit.edu.cn比较好.
```  
$ sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev ia32-libs x11proto-core-dev libx11-dev lib32readline5-dev lib32z-dev
```
然后配置环境变量
```  
$ mkdir ~/bin
$ PATH=~/bin：$PATH
```
然后通过curl下载repo脚本
```  
$ curl http：//android.git.kernel.org/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
[/code
开始创建存放Android源码目录
```  
$ mkdir directory
$ cd directory
```
开始初始化repo，如果我们下载1.5的源码，即cupcake，可以执行
```  
$ repo init -u git：//android.git.kernel.org/platform/manifest.git -b cupcake
```
接下来会提示输入你的用户名和邮箱，如果你需要上传Android源码分支，这个邮箱必须填写gmail账户，然后开始同步源码，就是下载Android源码
```  
$ repo sync
```
这里Android开发网通过分析repo脚本发现有个多线程参数，为-j
如果开启10个线程下载可以执行
```  
$ repo sync -j 10
```
如：在修改了某一个模块以后，可以使用 $ mmm <目录> 来重新编译所有在<目录>中的所有模块，使用$ mm 编译当前目录中的所有模块。
编完之后，即修改了Android系统以后，可以使用 $ make snod 重新生成system.img