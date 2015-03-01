1) 安装distcc RPM包
```  
rpm –ivh distcc-2.18.3-2.el5.rf.i386.rpm
rpm –ivh distcc-server-2.18.3-2.el5.rf.i386.rpm
```
2) 让系统启动时默认加载distccd服务进程
shell->setup->system service->distccd 选中该选项
启动distccd服务
/etc/rc.d/init.d/distccd start
3) 复制编译器到指定系统目录
参与分布式编译的机器都需要在特定的目录下安装交叉编译器，
arm-linux-4.1.1 ->/usr/local  kernel编译需要使用此编译器
prebuilt  ->/usr/local android编译需要此编译器
4)修改配置文件
```  
vi /etc/sysconfig/distccd 增加如下几行
OPTIONS=”—NICE10 –JOBS5 –allow 192.168.0.0/16”
USER=”distcc”
mkdir /etc/distcc/
vi /etc/distcc/hosts  将参与分布式编译的主机加入到hosts列表,比如
localhost/1 192.168.80.3/1  192.168.80.5/1…
```
5)创建distcc主目录
```  
mkdir –p /home/distcc
chown distcc.distcc /home/distcc
```
6) 开始编译源代码
```  
cd cupcake
. build/envsetup.sh
```
tapas 该命令会弹出配置选项
配置完成后即可开始编译
Make –j20 20表示20 个线程同时进行编译
搭建分布式编译器的机器有两台,安装ubuntu9.04操作系统，IP地址分别是192.168.0.222(A)，192.168.0.111(B)。两台机器上都有arm-eabi-4.4.0编译器（可以从/android/prebuilt/toolchain/目录下copy），放在目录/usr/local/下。A机器提供编译服务，B机器作为客户端
1. Server和client端都要做的操作：
1）首先确认server和client上都安装了distcc
sudo apt-get install distcc
安装distcc图形界面监测程序（不是必须）
apt-get install distccmon-gnome
2）设置编译器路径到系统PATH中：
export PATH=/usr/local/arm-eabi-4.4.0/binPATH
可以把此命令添加到~/.bashrc脚本中，这样每次启动都自动设置好了。
3）sudo vi /etc/default/distcc
修改STARTDISTCC的值为true。
STARTDISTCC="true"
在A（server）上，
修改 ALLOWEDNETS="192.168.0.0/16"
LISTENER="192.168.0.215" （server的ip）
在B（client）上，不修改以上ALLOWEDNETS和LISTENER的值。
其中ALLOWEDNETS采用了CIDR地址规则，在这里，192.168.0.0/16表示只要前16位为192.168的合法IP地址都可以请求编译服务。
针对B机器，由于不对外提供编译服务，所以只要将STARTDISTCC设为true即可。
4）sudo vi /etc/init.d/distcc
将/usr/local/arm-eabi-4.4.0/bin添加到distcc的PATH变量中。
5）在/usr/lib/distcc中建立distcc的软链接
A、B机器作相同的操作
```  
cd /usr/lib/distcc
sudo ln –s ../../bin/distcc arm-eabi-gcc
sudo ln –s ../../bin/distcc arm-eabi-g++
…
```
/usr/local/arm-eabi-4.4.0/bin/下面的所有编译工具都要作到/usr/lib/distcc目录下相应的链接。
2. 另外在client需要完成：
export PATH=/usr/lib/distccPATH
要保证/usr/lib/distcc在系统PATH的最前面。
export DISTCC_HOSTS=”192.168.0.222 192.168.0.211” （可添加多个server的ip）
3. server重启distcc服务
sudo /etc/init.d/distcc restart （此步骤只需在server端执行，client端并不启动distcc服务）
之后，在client上开始编译android源码，可用distccmon-gnome来监视编译过程。