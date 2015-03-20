apk文件实际是一个zip压缩包，可以通过解压缩工具解开。
以下是我们用zip解开helloworld.apk文件后看到的内容，可以看到其结构跟工程结构有些类似，如下图所示：
```  
|-- AndroidManifest.xml
|-- META.INF
| |-- CERT.RSA
| |-- CERT.SF
| |-- MANIFEST.MF
|-- classes.dex
|-- res
| |-- drawable
| | |--icon.png
| |-- layout
| | |--main.xml
|-- resources.arsc
```
#### Manifest 文件         
AndroidManifest.xml是每个应用都必须定义和包含的，它描述了应用的名字、版本、权限、引用的库文件等等信息，如要把apk上传到Google Market上，也要对这个xml做一些配置。
#### META-INF目录         
META-INF目录下存放的是签名信息，用来保证apk包的完整性和系统的安全。在eclipse编译生成一个api包时，会对所有要打包的文件做一个校验计算，并把计算结果放在META-INF目录下。而在Android平台上安装apk包时，应用管理器会按照同样的算法对包里的文件做校验，如果校验结果与META-INF下的内容不一致，系统就不会安装这个apk。这就保证了apk包里的文件不能被随意替换。比如拿到一个apk包后，如果想要替换里面的一幅图片，一段代码，或一段版权信息，想直接解压缩、替换再重新打包，基本是不可能的。如此一来就给病毒感染和恶意修改增加了难度，有助于保护系 统的安全。
#### classes.dex文件         
classes.dex是java源码编译后生成的java字节码文件。但由于Android使用的dalvik虚拟机与标准的java虚拟机是不兼容的，dex文件与class文件相比，不论是文件结构还是opcode都不一样。目前常见的java反编译工具都不能处理dex文件。
Android模拟器中提供了一个dex文件的反编译工具dexdump。用法为首先启动Android模拟器，把要查看的dex文件用adb push上传的模拟器中，然后通过adb shell登录，找到要查看的dex文件，执行dexdump xxx.dex。
目前在网上能找到的另一个dex文件的反编译工具是Dedexer。Dedexer可以读取dex格式的文件，生成一种类似于汇编语言的输出。这种输出与jasmin[]的输出相似，但包含的是Dalvik的字节码。
#### res 目录         
res目录存放资源文件。
#### resources.arsc    
编译后的二进制资源文件。
