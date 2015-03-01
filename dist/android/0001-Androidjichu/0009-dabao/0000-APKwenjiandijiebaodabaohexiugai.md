相信每位玩机的人对APK文件都不陌生。你可能每天都与APK文件打交道，无论是安装和卸载有用的应用工具、插件、好玩的游戏等等。。。你可曾知道这些每天都伴随着你的APK文件是什么吗？怎样对它们作些修改呢？比如说：对英文版进行汉化、修改功能、修改文字描述、去掉广告等等。本文介绍APK的基本知识、结构、APK文件的解包、打包及签名，以及对APK文件的常规修改。
#### APK文件简介
APK是Android Package的缩写，即即Android application package文件或Android安装包。每个要安装到Android平台的应用都要被编译打包为一个单独的文件，后缀名为.apk。APK文件是用专业软件eclipse编译生成的文件包，其中包含了应用的二进制代码、资源、配置文件等。通过将APK文件直接传到Android手机中执行即可安装。APK文件其实就是zip格式，但其扩展名被改为apk，用解压软件可以直接打开。通过WinRAR或UnZip解压后，你会看到有几个文件和文件夹。一个典型的APK文件通常有下列内容组成：
```  
AndroidManifest.xml 程序全局配置文件
classes.dex    Dalvik字节码
resources.arsc    编译后的二进制资源文件
META-INF\ 该目录下存放的是签名信息
res\  该目录存放资源文件
assets\ 该目录可以存放一些配置文件
```
下面对这些文件和目录做些基本的注释和介绍。
1、AndroidManifest.xml
该文件是每个应用程序都必须定义和包含的文件，它描述了应用程序的名字、版本、权限、引用的库文件等等信息。需要解包后才能加以阅读。
2、classes.dex文件 
classes.dex是java源码编译后生成的java字节码文件。dex是Dalvik VM executes的全称，即Android Dalvik执行程序，并非Java ME的字节码而是Dalvik字节码。
3、resources.arsc 
编译后的二进制资源文件。
#### META-INF目录 
META-INF目录下存放的是签名信息，用来保证apk包的完整性和系统的安全。在eclipse编译生成一个apk包时，会对所有要打包的文件做一个校验计算，并把计算结果放在META-INF目录下。这就保证了apk包里的文件不能被随意替换。比如拿到一个apk包后，如果想要替换里面的一幅图片，一段代码， 或一段版权信息，想直接解压缩、替换再重新打包，基本是不可能的。如此一来就给病毒感染和恶意修改增加了难度，有助于保护系统的安全。
4、res目录 
res目录存放资源文件。包括图片，字符串等等。
解包后，几乎所有可能的修改和编辑工作基本都在这里。
5、assets目录
assets目录可以存放一些配置文件，这些文件的内容在程序运行过程中可以通过相关的API获得。 
#### APK文件的解包和打包
APK文件是用专业软件eclipse编译生成的文件包。在网上可以找到许多软件来对APK的内容进行反编译，例如：可以通过AXMLPrinter2工具和命令：java -jar AXMLPrinter2.jar AndroidManifest.xml 解开在apk中的AndroidManifest.xml。
最近，业界有一个功能强大的解包打包工具包apktool，可以在Windows下用来方便快速地对APK文件进行解包和打包，给修改和编辑工作带来许多方便。下面来介绍它的使用。
1) APKtool软件包
APKtool软件包有2个程序组成：apktool.jar 和 aapt.exe
另外提供一个批处理文件：apktool.bat，其内容为：
```  
java -jar "%~dp0\apktool.jar" %1 %2 %3 %4 %5 %6 %7 %8 %9
```
运行apktools.jar需要java环境（1.6.0版本以上）。
apktool.jar用于解包，apktool.jar和aapt.exe联合用于打包。
2) APK文件的解包
下面以解开Contacts.apk为例。首先把Contacts.apk复制到当前工作目录下（例：Test）。在DOS下打入命令：
```  
apktool d Contacts.apk ABC
```
这里“d"表示要解码。Contacts.apk是要解包的APK文件。ABC是子目录名。所有解包的文件都会放在这个子目录内。
3) APK文件的打包
在DOS下打入命令
```  
apktool b ABC New-Contacts.apk
```
这里“b"表示要打包
ABC是子目录名，是解包时产生的子目录，用来存放所有解包后的和修改后的文件。
New-Contacts.apk是打包后产生的新的APK文件。
4) Framework框架文件
在解开APK文件时，apktool需要框架文件（framework-res.apk）来解码和打包。Apktool已经包含了标准的框架，所以在大多数APK文件的解包时，不需要另外提供框架文件。但是，某些制造商使用了他们自己的框架文件，为了解包，就不得不从手机中把框架文件（framework-res.apk）提取出来，然后安装到计算机。安装命令是：
```  
apktool if framework-res.apk 
```
安装后就会得到：~\apktool\framework\1.apk
5) 解包、解包和签名批处理
在实际使用时，可能对多个APK文件进行处理。方便的做法是写成批处理文件。打包和签名可以一次完成。
解包批处理命令：
```  
for %%i in (*.apk) do java -jar apktool.jar d %%i _%%i && move _%%i Modifying_Files && copy %%i Backuped_Raw_Files && @echo File [%%i] unpacking process is completed!
```
打包和签名批处理命令：
```  
for /d %%i in (*) do cd.. && java -jar apktool.jar b Modifying_Files\%%i && java -jar signapk.jar testkey.x509.pem testkey.pk8 Modifying_Files\%%i\dist\*.apk %%i && ren %%i New%%i && move New%%i Modified_Signed_Files && @echo %%i Complete repacking and Signing! && cd Modifying_Files
```
#### 应用实例：APK解包后的编辑和修改
为什么要对APK文件进行解包？当然要对其内容进行必要的修改。修改什么呢？通常，如果只是对图像进行替换，没有必要进行解包和打包。用WinRAR打开APK文件，直接做替换就可以了。但是，如果要对文字和其它非图像类内容进行修改，那只能通过解包解码了。下面几项任务需要对APK进行解包和打包。这里只作大概介绍，请自己去找详细的操作方法和教程。
#### 1) 汉化APK软件
在res文件夹中，我们可以看到有很多values-***的文件夹，这就是语言包。values是英文语言包，values-zh是中国地区语言包（包含港澳台及内地），values-zh-rCN是中文简体语言包（只包含内地），values-zh-rTW是中文繁体语言包（港澳台）。除此以外，其它地区的语言包都是精简的对象，可以不过多了解。
在values文件夹里，通常有arrays.xml、strings.xml等语言文件，要作汉化就要对这些文件进行修改。有时也需要修改其它xml文件，一个一个地认真查看。
#### 2) 修改图标标签
每一个APK文件都有一个“图标标签"。将APK程序安装进手机后，在图标下面显示图标标签文字。这个图标标签的内容是可以修改的。在\res\values下找到strings.xml，修改其中的一行：
<string name="app_name">图标标签</string>
例如：<string name="app_name">静音启动</string>
同理，如果是窗口小插件，要修改widget_name。
注意：system/app下的apk不宜修改，因为要同时修改对应的odex文件。
#### 3) 去掉APK中的广告
有很多APK应用都带有广告。为了去掉程序中的广告，要修改main.xml文件与广告有关的内容。在\res目录下找到文件main.xml。通常在\layout目录下，有时也被放在其它目录下。甚至，有时不存在main.xml文件，广告行被放在其它xml文件内。只能细心逐个文件进行查找。无论哪一种情况，查看其内容，你会看到有一项类似的命令如下。这就是广告显示。
```  
<com.admob.android.ads.AdView 
	android:id="@id/ad"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	admobsdk:backgroundColor="ff000000″
	admobsdk:textColor="ffffffff"
	admobsdk:keywords="Android application" /> 
```
将其改为：
```  
<com.admob.android.ads.AdView 
	android:id="@id/ad"
	android:layout_width="0.0dip"
	android:layout_height="0.0dip"
	admobsdk:backgroundColor="ff000000″
	admobsdk:textColor="ffffffff"
	admobsdk:keywords="Android application" /> 
```
可以看到，关键是要把fill_parent改为0.0dip，把wrap_content改为0.0dip，其它保持不变即可。这种改法就是不给广告显示空间，当然你就看不到广告了。
#### 4) 修改显示电池为1%精度
在XT502上，默认显示只有7档: 0%，10%，20%，40%，60%，80% 和100%。通过修改framework-res.apk，可以改变显示精度。但是在XT502上，实践证明最好可能达到的现实精度只有10%。修改工作如下：
(1) 对framework-res.apk进行解包
(2) 修改和增加电池状态图标
(3) 修改文件stat_sys_battery.xml
(4) 修改文件stat_sys_battery_charge.xml
(5) 打包
(6) 提取stat_sys_battery.xml，stat_sys_battery_charge.xml，resources.arsc和一个图标目录：drawable-mdpi
(7) 重新装配framework-res.apk
#### 5) 状态栏信息通知文字颜色修改
状态栏信息通知文字颜色，是由framework-res.apk文件里res\values下的colors.xml文件控制的，所以我们只需修改colors.xml文件就可以了。另外，此文件还控制下拉栏的文字颜色，可以修改。
用文本编辑器打开colors.xml文件，找到
<color name="hw_statusbar_time">ff000000</color> 
将这句修改为：<color name="hw_statusbar_time">ffffffff</color>
这状态栏信息通知文字颜色由黑色改为白色。