使用模块编译，只需要在你所在的模块的目录或者其子目录，执行mm，便可以编译出一个单独的apk，这样岂不快哉！
具体步骤：
1）打开~/.baserc文件，加入source ～/I850/build/envsetup.sh. 加入你自己该文件所在的路径，这样就免去了每次启动新的终端执行mm命令之前，需要引用此文件。
2）完成步骤1之后，就可以在你的模块里面随意执行mm了，要想使用其他快速命令，可以查看envsetup.sh文件，比如cgrep，jgrep，resgrep在不同类型的文件里面进行相应的查询.还有m，mmm等等。
3）还可以使用adb push 将你的apk push到模拟器或者手机终端，也可以在工程根目录通过make －snod生成新的system.img
编译模块
Android中的一个应用程序可以单独编译，编译后需要重新生成system.img。
在Android目录下运行
```  
$ .build/envsetup.sh 
```
或者
```  
$ source build/envsetup.sh
```
然后就会多出几个可用的命令：
```  
- croot：   Changes directory to the top of the tree.
- m：       Makes from the top of the tree.
- mm：      Builds all of the modules in the current directory.
- mmm：     Builds all of the modules in the supplied directories.
- cgrep：   Greps on all local C/C++ files.
- jgrep：   Greps on all local Java files.
- resgrep： Greps on all local res/*.xml files.
- godir：   Go to the directory containing a file.
- printconfig： 当前build的配置情况。
```
可以使用 --help查看用法。