@官方的文档地址：http://source.android.com/source/download.html（但可能会遇到点问题请看下面的讲解）
@系统要求：1 ubuntu 10.04或以上版本
2 64位系统（查看系统命令： uname -m 如果出现i386 i686 i586则是32位 如果出现amd64 则是64位系统 ）
3 jdk1.6 或更高版本
@说明：下面如果有修改文件不好保存或无法打开可能是权限问题要进入那个目录修改权限 chmod 777 filename
#### 1安装下载源码所需要的工具 
1.1
```  
sudo apt-get install git-core curl
```
这条命令会从互联网的软件仓库中安装 git-core 和 curl
1.2
```  
mkdir ~/bin
PATH=~/bin:$PATH
```
在home目录下建立bin目录并设置环境变量
1.3
```  
curl http://android.git.kernel.org/repo >~/bin/repo
```
这句命令会下载 repo 脚本文件到当前主目录的/bin 目录下,并保存在文件repo 中。
1.4
```  
chmod a+x ~/bin/repo
```
修改 repo 文件可执行权限
1.5 执行下面的命令创建并进入空文件夹
```  
mkdir yourdirectory
cd yourdirectory
```   
#### 2repo客户端初始化
下面是官网给的命令，但在公司行不通会报Connection timed out的错误，但在家直接用估计可以
```  
repo init -u git://android.git.kernel.org/platform/manifest.git -b cupcake
```
2.2 在公司同步要先将bin里的.repo文件的
```  
REPO_URL='git://android.git.kernel.org/tools/repo.git' 
```   
改成
```  
REPO_URL='http://android.git.kernel.org/tools/repo.git'
``` 
然后命令改成下面这个（注意后面的版本号写法和官网不太一样，如果写-b Gingerbread的话会找不到版本）
```  
repo init -u http://android.git.kernel.org/platform/manifest.git -b android-2.3.3_r1
``` 
(参考 http://blog.csdn.net/shaohui99/archive/2010/06/29/5702483.aspx)
2.3 执行上面的命令可能还会报个IOError找不到文件（暂时还不知道为什么）
但执行下面两条命令
```  
touch ~/.gitconfig
rm -rf .repo
``` 
后再执行2.2的命令就可以同步了
成功的话会叫你填写自己的名字和邮箱
#### 3下载源码
3.1 执行下面的命令会开始下载代码
```  
repo sync
``` 
如果也有 Connection timed out错误就找到你在1.5时创建的目录下找到.repo文件夹打开后找到下载清单manifest.xml（manifest.xml为隐藏文件，得显示隐藏文件后才能看见）
打开manifest.xml
修改
```  
fetch="git://android.git.kernel.org/"
``` 
为
```  
fetch="http://android.git.kernel.org/"（http的穿透）
``` 
然后再执行repo sync，成功后会下载代码要几个小时(我下载了一天)
#### 4编译
4.1 先进入1.5创建的空目录
再执行 make 命令
编译后的文件在out文件夹中
#### 5生成SDK
```  
make PRODUCT-sdk-sdk
``` 
#### 5生成SDK
make PRODUCT-sdk-sdk
编译完成后会在/work/froyo/out/host/linux-x86/sdk/目录生成sdk
![img](P)  
![img](P)  
![img](P)  
![img](P)  
![img](P)  
![img](P)  
![img](P)  
![img](P)  
![img](P)  
其实32位的也可以编译，这个是别人写的，我试了，成功，给大家参考一下
在使用：
```  
$ repo init -u git://Android.git.kernel.org/platform/manifest.git
$ repo sync
```
下载完代码后，进行make，
```  
$cd ~/mydroid
$make
```
却出现了如下错误：
```  
build/core/main.mk:73: You are attempting to build on a 32-bit system.
build/core/main.mk:74: Only 64-bit build environments are supported beyond froyo/2.2.
```
这是因为froyo/2.2默认只支持64-bit，看到有些网友还要去下载64-比他的操作系统，很是麻烦，于是通过不断搜索资料终于解决，
解决办法：
需要进行如下修改即可，
将
```  
./external/clearsilver/cgi/Android.mk 
./external/clearsilver/java-jni/Android.mk 
./external/clearsilver/util/Android.mk 
./external/clearsilver/cs/Android.mk
```
四个文件中的
```  
LOCAL_CFLAGS += -m64 
LOCAL_LDFLAGS += -m64 
```
注释掉，或者将“64”换成“32”
```  
LOCAL_CFLAGS += -m32 
LOCAL_LDFLAGS += -m32 
```
然后，将./build/core/main.mk中的
```  
ifneq (64,$(findstring 64,$(build_arch))) 
```
改为
```  
ifneq (i686,$(findstring i686,$(build_arch))) 
```
OK！问题解决。