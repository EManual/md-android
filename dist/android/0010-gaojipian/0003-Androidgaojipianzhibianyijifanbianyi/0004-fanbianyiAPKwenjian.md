反编译的目的在于学习一些优秀的Android应用程序代码。
在进行反编译之前，需要准备好下面的软件工具(这些文件都放在同一文件下)：
![img](P)  
这些工具的下载地址：http://down.51cto.com/data/266751
下面开始进行反编译APK文件：
1.先将上面的apktool-install-windows-2.1_r01-1.zip，dex2jar-0.0.7-SNAPSHOT.zip解压到一个盘的根目录的一个文件下面。（这里我选择D:\APKTool）
2.Win+R打开运行界面，输入cmd，进入dos窗口，输入cd /d D:\APKTool进入到D:\APKTool下面，然后输入下面的命令，按Enter键，会出现下图所示。
![img](P)  
apktool.jar是解包工具，d表示解包，android.apk是要解包的APK文件，红色矩形框表示解包后输出到这个文件夹。这时候打开d:\AndroidCode，就能看到通过解包得到的文件。
![img](P)  
里面的AndroidManifest.xml文件和res下面的所有文件就能直接打开查看了。
3.解包之后，将之前的android.apk文件的后缀名改为rar，之后就将里面的classes.dex文件解压到D:\APKTool下面。然后在dos窗口输入dex2jar.bat classes.dex。
![img](P)  
得到一个名为classes.dex.dex2jar.jar的文件，此时用jd-gui.exe打开classes.dex.dex2jar.jar或者用DJ Java Decompiler反编译工具将.class文件反编译成.java文件就能看到所有源代码了！
PS：APK文件反编译之后，XML的源码不会出现乱码，不过有些APK文件得到Java源码会出一些乱码(比如在给变量赋值的时候)，我现在没有到更好的解决方法，如果大家有的话，可以给我发邮件的。