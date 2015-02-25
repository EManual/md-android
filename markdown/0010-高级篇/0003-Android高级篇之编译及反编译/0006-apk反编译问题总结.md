今天反编译一个apk，遇到了很多问题，查了好几个文档，现在把前人的一些经验和自己碰到的问题记录下来，作一个总结，与大家分享，希望对大家有帮助。
#### 一、找到apk中的class.dex：
把apk文件改名为.zip，然后解压缩其中的class.dex文件，它就是java文件编译再通过dx工具打包成的。
#### 二、得到java源文件
工具准备：
1、把dex文件反编译为jar文件的工具。（dex2jar）
http://code.google.com/p/dex2jar/downloads/list 
2、把jar反编译为java的工具。（JD-GUI）
http://java.decompiler.free.fr/?q=jdgui
反编译步骤：
1、把class.dex拷贝到dex2jar.bat所在目录，直接拖动class.dex到dex2jar.bat，生成classes.dex.dex2jar.jar。
或者：1.在cmd下进入dex2jar.bat所在路径，然后输入“dex2jar.bat XXX”，XXX指的是你要反编译的apk中的classes.dex文件所在路径及名称，比如：我的dex2jar.bat在D:\Android\apk_decode\dex2jar-0.0.7-SNAPSHOT路径下, classes.dex在D:\Android下，所以： 你进入dex2jar.bat路径下后，输入dex2jar.bat D:\Android\classes.dex，这样会生成一个jar文件。
2.用rar解压出jar文件中的class文件，然后用jad或DJ Java Decompiler反编译工具将.class文件反编译成.java文件。
3、运行JD-GUI工具（它是绿色无须安装的），打开上面的jar文件，在File下有个Save JAR Source，它可以生成src源代码。
#### 三、上面操作只能得到class文件，下面利用Google提供的apktool得到xml文件。
1. 下载apktool，可以去Google的官方下载，地址：http://code.google.com/p/android-apktool/得，apktool-1.0.0.tar.bz2和apktool-install-windows-2.1_r01-1.zip两个包都要下。解压apktool-1.0.0.tar.bz2得到apktool.jar放到 C:\Windows ，解压apktool-install-windows.zip到任意文件夹(例如E盘根目录)。（我是两个包解压后都放在C:\Windows下）
2. Win+R 运行CMD，用cd命令转到apktool-install-windows所在文件夹，输入apktool看看。会列出一些帮助的话就成功了（解释d为加压 第一个路径为你的apk所在的位置。第二个是要输出的位置）。
在这一步中我碰到这样一个问题：
```  
Exception in thread "main" java.lang.UnsupportedClassVersionError: Bad version n
umber in .class file
at java.lang.ClassLoader.defineClass1(Native Method)
at java.lang.ClassLoader.defineClass(ClassLoader.java:620)
at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:12
4)
at java.net.URLClassLoader.defineClass(URLClassLoader.java:260)
at java.net.URLClassLoader.access$100(URLClassLoader.java:56)
at java.net.URLClassLoader$1.run(URLClassLoader.java:195)
at java.security.AccessController.doPrivileged(Native Method)
at java.net.URLClassLoader.findClass(URLClassLoader.java:188)
at java.lang.ClassLoader.loadClass(ClassLoader.java:306)
at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:268)
at java.lang.ClassLoader.loadClass(ClassLoader.java:251)
at java.lang.ClassLoader.loadClassInternal(ClassLoader.java:319)
```
版本问题，装了个jre6，在360中的软件管家可以找到的，记得装完后配置path路径，果然，ok。
继续:
```  
apktool d e:\a.apk（apk路径）ABC（文件夹名称）。
```
这时当前目录下生成 ABC文件夹，里面就是我们想要的东东了。
#### 四、将“二”中得到的java文件和“三”中得到的xml文件组合成一个android工程，即可得到完整的apk源码。