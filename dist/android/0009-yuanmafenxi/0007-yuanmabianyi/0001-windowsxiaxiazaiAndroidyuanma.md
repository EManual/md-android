首先下载cygwin，cygwin是一个类linux平台。即在windows环境下模拟linux终端。比起运行linux虚拟机，是一个轻量的解决办法。除了本文用来下载android源码，你当然可以用它来学习linux。cygwin的中文网是http://www.cygwin.cn/，建议从这下载cygwin，这是国内最快的镜像站点。严格按网站的说明安装：http://www.cygwin.cn/site/install/，最后注意的是在安装说明的下一步操作是选择需要下载的工具库，缺省是是default，鼠标点击default，会把安装类别切换成install，这样才能安装下载android源码需要的所有工具。当然，如果你熟悉所有情况，你可以手工在工具库里选择你要安装的库（库是很多的，安装程序又没有提供全部选择或者全部取消的功能，我奇怪linux有关的程序总是有这种类似的遗漏。） 
安装完cygwin后，运行它。会出现一个类linux的环境。输入 
```  
$mkdir /home/android　//创建工作目录 
$cd /home/android 
$mkdir bin 
//下载安装repo版本管理工具： 
$curl http://android.git.kernel.org/repo> /home/android /bin/repo 
```
注意：在这步骤中。我出现bash: curl: command not found错误，在网上搜索了好久的资料，大概说是没有装curl.几经周折，终于找到一篇比较有用的帖子：内容如下：
```  
So I searched for the curl-7.20.1-1 cygwin on Google. I found this helpful site mirrors.xmission.com/cygwin/release/curl/
That site had a link to download curl-7.20.1-1.tar.bz2. I unzipped it using 7zip. It unzips it into ./user/bin/ or something so I had to find curl.exe in the local /usr/bin folder and put it into my /bin folder of c:\cygwin
```
看了大家都知道了吧！在上面那个网址中下载压缩文件curl-7.20.1-1.tar.bz2，然后解压，将/usr/bin下面的curl.exe拷贝到c:\cygwin\bin（这是cygwin的安装路径）下面。照着这样做了之后，在执行上面的命令，就OK，成功通过了。$cd bin 
$chmod a+x repo 
准备下载Android： 
```  
$cd /home/android 
$python ./bin/repo init -u git://android.git.kernel.org/platform/manifest.git –bcupcake 
$git config --global user.email \"xxxxx@xxxxxxx\" 
$git config --global user.name \"xxxxxx\" 
```
邮箱地址填有效邮箱即可，我试过，其实这步跳过也没有问题。 
下载源码： 
```  
$python repo sync 
```
唯一和linux下不同的地方是该环境似乎没有内嵌支持python，因此需要用命令行python来调用repo脚本。