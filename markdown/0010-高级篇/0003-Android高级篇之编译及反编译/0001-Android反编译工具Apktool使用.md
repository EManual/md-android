Android反编译工具：Apktool，支持Linux 、Windows下工作。
安装步骤如下：
1.首先安装需要JAVA环境，先下载JDK/JRE，点击下载，已经有JAVA环境的可跳过此步。
2.到code.google上下载apktool.jar以及相关文件：http://code.google.com/p/android-apktool/downloads/list
点击下载apktool-1.0.0.tar.bz2 和apktool-install-windows-2.1_r01-1.zip。
3.解压apktool.jar 到 C:\Windows文件夹下。
解压apktool-install-windows.zip到任意文件夹。
4.点击开始菜单，运行，输入CMD回车，用cd命令转到刚刚解压apktool-install-windows所在的文件夹，输入apktool，出现一些命令说明即成功安装。
Apktool 命令
```  
apktool d geek.apk test  反编译 geek.apk到文件夹test
apktool b  geek 从文件夹geek重建APK，输出到ABC\dist\out.apk
```