Android SDK包含了各种各样的定制工具，简介如下：
#### Android模拟器（Android Emulator）
它是在你的计算机上运行的一个虚拟移动设备。你可以使用模拟器来在一个实际的Android运行环境下设计，调试和测试你的应用程序。
Android调试桥（Android Debug Bridge (adb) ）
#### Adb 
工具可以让你在模拟器或设备上安装应用程序的.apk文件，并从命令行访问模拟器或设备。你也可以用它把Android模拟器或设备上的应用程序代码和一个标准的调试器连接在一起。
#### 层级观察器 （Hierarchy Viewer）
层级观察器工具允许你调试和优化你的用户界面。它用可视的方法把你的视图（view）的布局层次展现出来，此外还给当前界面提供了一个具有像素栅格(grid)的放大镜观察器，这样你就可以正确地布局了。
#### 9-patch
Draw 9-patch工具允许你使用所见即所得（WYSIWYG）的编辑器轻松地创建NinePatch图形。它也可以预览经过拉伸的图像，高亮显示内容区域。
Eclipse IDE Android 开发工具插件（Android Development Tools Plugin for the Eclipse IDE）
ADT插件大大扩展了Eclipse集成环境功能，使得生成和调试你的Android应用程序既容易又迅速。如果你使用Eclipse，ADT插件可以让你难以置信地加快开发Android应用程序的 速度。
你可以从Eclipse IDE内部访问其它Android开发工具。例如，ADT可以让你直接从Eclipse访问DDMS工具的很多功能—屏幕截图，管理端口转发（port-forwarding）,设置断点，观察线程和进程信息。
它提供了一个新的项目向导（New Project Wizard），帮助你快速生成和建立起新Android应用程序所需的最基本的文件。
它使得构建Android应用程序的过程变得自动化以及简单易行。
它提供了一个android代码编辑器，可以帮助你为Android manifest和资源文件编写有效的XML。
有关ADT插件的更多详细信息，包括安装指令，可参考Android 开发环境安装。如果你想看一个用法范例的屏幕截图，可参考Hello Android。
#### Dalvik 调试监视器服务（Dalvik Debug Monitor Service (ddms)）
这个工具集成了Dalvik（为Android平台定制的虚拟机（VM）），能够让你在模拟器或者设备上管理进程并协助调试。你可以使用它杀死进程，选择某个特定的进程来调试，产生跟踪数据，观察堆（heap）和线程信息，截取模拟器或设备的屏幕画面，还有更多的功能。
#### Android Asset Packaging Tool (aapt)
Aapt工具可以让你创建包含Android应用程序二进制文件和资源文件的.apk文件。
Android接口描述语言（Android Interface Description Language (aidl)）
可以让你生成进程间的接口的代码，诸如service可能使用的接口。
#### sqlite3
这个工具能够让你方便地访问SQLite 数据文件。这些数据文件是由Android 应用程序创建并使用的。
#### Traceview
这个工具可以将你的Android 应用程序产生的跟踪日志（trace log）转换为图形化的分析视图。
#### mksdcard
帮助你创建磁盘映像（disk image），你可以在模拟器环境下使用磁盘映像来模拟外部存储卡（例如SD 卡）。
#### dx
Dx gongju 将.class字节码（bytecode）转换为Android字节码（保存在.dex文件中） 。
#### UI/Application Exerciser Monkey
Monkey是在模拟器上或设备上运行的一个小程序，它能够产生为随机的用户事件流，例如点击(click)，触摸(touch)，挥手（gestures），还有一系列的系统级事件。你可以使用Monkey来给你正在开发的程序做随机的，但可重复的压力测试。
#### activitycreator
一个可以产生Ant build 文件的脚本，你可以使用它编译你的android 应用程序。如果你正在Eclipse上开发，并使用ADT插件，你不必使用这个脚本。