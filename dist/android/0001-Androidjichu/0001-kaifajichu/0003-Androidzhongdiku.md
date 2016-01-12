比起java类库的惨不忍睹，android的类库可以说很对的起观众了，下面是具体的类库：
1、Android.util
核心使用包（看名字就知道啦），包括了低级类，例如，专用的容器、字符串格式化和XML解析程序。

2、Android.os
操作系统包，提供了基本操作系统服务的访问时间，例如，消息传递、进程间通信、始终函数和调试。

3、Android.graphic
图形API提供了支持画布、颜色和绘画原语的低级图行类，让你可以在画布上画画。

4、Android.text
用来显示和解析文本的文本处理工具。

5、Android.database
当使用数据库的时候，提供处理游标（cursor）所要求的低级类。

6、Android.content
内容API通过处理资源、内容提供器和包的服务，来管理数据访问和发布。

7、Android.view  
View是核心用户界面类。所有的用户界面元素都是使用一系列View来构造的，用来提供交互组件。

8、Android.widget
构建在View包的基础上，Widget类是已经创建好的用户界面元素，可以直接在应用程序中使用，他们包含列表、按键和布局。

9、Com.google.android.maps
一个高级API，提供了对本地地图控件的访问，可以再应用程序中使用这些控件，它包括MapView控件以及用来对嵌入的地图进行注释和控制的Overlay和MapController类。

10、Android.app  
一个提供了对应用程序模型进行访问的高级包。应用程序包包含活动（Acitivity）和服务（Service）API，它形成所有应用程序的基础。

11、Android.provider
为了方便开发人员对某些标注的内容提供器进行访问，provider包提供了一些类，从而提供了对所有的android发行版中包含的标准数据库的访问。

12、Android.telephony telephony
Api允许直接与蛇鞭的电话栈进行交互，让你可以直接打电话、监控电话状态以及收发SMS消息。

13、Android.webkit webKit
包提供了与基于Web的内容相关的API，包括一个WebView控件，可以再活动或许cookie管理器重嵌入一个浏览器。
除了Android API之外，android栈还包含了一些可以提供程序框架使用的c/c++库集合：

1. OpenGL  基于OpenGL ES ApI 的用来支持3D图形的库。
2. Free Type  支持位图和矢量字体渲染。
3. SGL 用来提供2D图形引擎的核心库。
4. Libc 为基于Linux的嵌入式设备而优化的标注C库。
5. SQLite 用来存储数据的轻量级关系数据库引擎
6. SSL 用来支持使用安全套接字层（Secure Sockets Layer）加密协议的安全Internet通信。
7. Android.location 基于位置的服务API让应用程序可以访问到设备当前的物理位置，不管使用什么样硬件或技术来确定位置，基于位置的服务都提供了对位置信息的通用访问。
8. Android.media 媒体API提供了对音频和视频文件的回收和录制的支持，包括流媒体。
9. Android.opengl android使用OpenGL ES API提供了强大的3d渲染引擎，这种酒可以使用它来创建动态的3D用户界面。
10. Android.hardware 当可用的时候，硬件API就会提供传感器硬件，包括摄像头、加速计和罗盘传感器。
11. Android.buetooth, android.net.wifi和android.telephony  android也提供了对硬件平台的低级访问，包括蓝牙，wifi和电话硬件。
