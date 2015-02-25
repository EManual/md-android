总结反编译主要的目的在于学习。利用反编译进行相关的汉化或修改，都是不道德的！
大家都知道，将apk文件解压后有两部分文件需要处理，一种是xml文件，另一种一个dex文件（.dex），我们可以从.dex文件中得到.class，利用后者再得到大家垂涎已久的java文件。
下面分别针对这三种格式的文件进行反编译处理；
1.对xml文件进行包的解析，一般有两种方式:apktool（推荐）和AXMLPrinter2.jar;
2.从dex到class公认dex2jar.bat，实现反编译；公认的强者；
3.而class到java的方式要更多样化一些,因为只是查看反编译后的代码：jd-gui（推荐），Jodeclipse（Jode的Eclipse插件），JadClipse（Jad的Eclipse插件）。
还是作个大致介绍吧：
1.首先把apk文件改名为.zip，然后解压缩其中的class.dex文件，它就是java文件编译再通过dx工具打包成的。
2.把class.dex拷贝到dex2jar.bat所在目录。运行dex2jar.bat class.dex，生成classes.dex.dex2jar.jar。
3.运行JD-GUI工具（绿色软件，好用的软件！），打开上面的jar文件，即可看到java源代码。
如果上面的步骤都可以自我完成了，那么，下面内容就可忽略不看了！
这几个软件，细分开来介绍（用步骤A(分A1,A2), B, C(分C1,C2,C3), ABC分别代表三个不同的步骤）：
#### A1. apktool:
通常用于生成程序的源代码和图片、XML配置、语言资源等文件。我们对图片和语言资源等文件修改后，可以再把它们编译打包成APK，签名后就是手机可以安装的本地化/修正版APK了。支持Linux 、Windows下工作。
安装步骤：
1.安装JAVA环境（官方推荐jdk 1.6）；
2.下载apktool.jar：http://code.google.com/p/android-apktool/downloads/list
点击下载apktool1.3.2.tar.bz2  和apktool-install-windows-2.2_r01-3.tar.bz2 (不一定是这个，但最好选最新版本的吧！)；
3.解压apktool1.3.2.tar.bz2得到apktool.jar；
解压apktool-install-windows.zip到任意文件夹，将apktool.jar拷入此文件夹中（也有人说是直接全部拷入C:/Windows，一样的）；
（目前此文件夹中有三个文件：apktool.jar/apktool.bat/aapt.exe）
4.cmd命令行进入到解压apktool-install-windows-2.2_r01-3.tar.bz2所得的文件夹，输入apktool测试是否安装成功；
安装成功后，下面开始反编译过程：
#### 1.apktool d （要反编译的文件） （输出文件夹）
如：
```  
apktool d XXX.apk （目标文件夹）      反编译 geek.apk到文件夹test
```
#### 2.apktool b （目标文件夹）              
从目标文件夹中重建APK，生成的APK在"目标文件夹"\dist文件夹里，叫out.apk。
这个out.apk是没有签名的，所以不能直接装到手机里。签名工具和方法见http://www.hiapk.com/bbs/thread-21261-1-1.html，这里就不说了。签名后得到的APK，就是可以装到手机里的了。
#### A2. AXMLPrinter2.jar
将它放到android-sdk-windows-1.5_r3\tools文件夹中。运行cmd,进入tools目录，运行java -jar AXMLPrinter2.jar main.xml > main.txt；
于是我们就得到了反编译后的XML文件。
经历了这么多，我们得到的只是部分布局文件和资源文件，但java文件还是"犹抱琵琶半遮面"。
下面，让我们掀起她的红盖头来: 
#### B. dex2jar
下载：http://code.google.com/p/dex2jar/downloads/list 
方法：
1.首先找到Android软件安装包中的classes.dex （解压得到）;
它就是java文件编译再通过dx工具打包成的,所以现在我们就用上述提到的2个工具来逆方向导出java源文件。
2.把classes.dex拷贝到dex2jar.bat所在目录;
在命令行模式下定位到dex2jar.bat所在目录，运行 dex2jar.bat classes.dex，生成classes.dex.dex2jar.jar，成功了一半！
#### C1. JD-GUI
下载：http://java.decompiler.free.fr/?q=jdgui
方便好用，直接解压得到JD-GUI，用它打开上面的jar文件，File-->Save JAR Source，即可看到梦寐以求的java源代码。
我们也可以解压B步骤得到的jar文件得到class文件，到这里，我们就要用到Jodeclipse和JadClipse了。
#### C2. Jodeclipse---Jode的Eclipse插件  C3. JadClipse---Jad的Eclipse插件
关于这两个Eclipse插件的安装可见下面链接：
http://tgyd2006.javaeye.com/blog/553061
(C4. 还有朋友提到DJ Java Decompiler，没用过，可以一试！）
但也有人提出此问题：
自从eclipse升级到3.3以后jad插件就一直没有成功的安装上去，网上看了好多文章也是以前版本的安装方法，3.3目前通过eclipse的software update的插件安装方式已经不行了。 
解决方法如下： 
1.从http://www.kpdus.com/jad.htmldownload地址下载最新的jad，我目前下载的是jadnt158.zip。
2.从http://nchc.dl.sourceforge.net/s ... jadclipse_3.3.0.jar地址下载jadclipse_3.3.0.jar，拷贝到eclipse的plugins目录下。
3.启动或重起eclipse，修改window -> Preferences -> Java -> JadClipse 下的Path to decompiler 如：D:\eric\jadnt158\jad.exe（jadnt158.zip解压后的目录）。
4.Windows -> Perference -> General -> Editors -> File Associations中修改“*.class”默认关联的编辑器为“JadClipse Class File Viewer” 大功告成，之后在java类里按住ctrl点击类就可以看到它jad反编译后的源带码了。
如果发现安装了没有效果，可以删除eclipse主目录下的\configuration\org.eclipse.update后，再执行eclipse -clean试试。
最后，将得到的java文件和得到的xml文件组合可得一个android工程，即可得到相对比较完整的apk源码；但也有些额外加的包没被编译出来。
但做到这一步已经足够用于学习，我们的目的也就达到了！