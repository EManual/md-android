在android中所涉及的概念和代码最多，最繁杂的就是GDI相关的代码了。但是本质从抽象上来讲，这么多的代码和框架就干了一件事情：对显示缓冲区的操作和管理。
GDI主要管理图形图像的输出，从整体方向上来看，GDI可以被认为是一个物理屏幕使用的管理器。因为在实际的产品中，我们需要在物理屏幕上输出不同的窗口，而每个窗口认为自己独占屏幕的使用，对所有窗口输出，应用程序不会关心物理屏幕是否被别的窗口占用，而只是关心自己在本窗口的输出，至于输出是否能在屏幕上看见，则需要GDI来管理。
![img](P)  
从最上层到最底层的数据流的分析可以看到实际上GDI在上层为GUI提供一个抽象的概念,就好像操作系统中的文件系统所提供文件，目录等抽象概念一样，GDI输出抽象成了文本,画笔,位图操作等设备无关的操作,让应用程序员只需要面对逻辑的设备上下文进行输出操作,而不要涉及到具体输出设备,以及输出边界的管理。GDI负责将文本、线条、位图等概念对象映射到具体的物理设备，所以GDI的在大体方向上可以分为以下几大要素：画布、字体、文本输出、绘画对象、位图输出。
Android的GDI系统所涉及到概念太多，加之使用了OpenGL使得Android的层次和代码很繁杂。但是我们对于Android的GDI系统需要了解的方面不是他的静态的代码关系，而是动态的对象关系，在逻辑运行的架构上理解GDI。我们首先还是需要从代码结构开始我们的理解。
```  
Frameworks/Libs/Surfaceflinger
Frameworks/base/core/jni/android_view_Surface.cpp
Frameworks/base/core/java/android/view/surface.java
Frameworks/base/Graphics：绘图接口
Frameworks/Libs/Ui
External/Skia
```
其中External/Skia是一个C++的2D图形引擎库，Android的2D绘制系统都是建立在该基础之上.Skia完成了：文本输出，位图，点，线，图像解码等功能。
我在这里给出Android GDI的基本框架示意图。
![img](P)  
对于上面的GDI架构图我们只是一个大概的了解，我们有太多的问题需要解决，有太多的疑问需要得到答案，我就一直在想，为什么设计者有提出如此众多的概念，这个概念的背景是什么？他要管理什么，他要抽象什么？从前面知道，Android的整个设计理念就是无边界化，他是如何穿透Linux进程这个鸿沟来达到无边界的？Surface，Canvas， Layer，LayerBase, NativeBuffer，SurfaceFlinger，SurfaceFlingerClient这些到底是一个什么东西？如何管理，传递的是什么？创建的是什么？这些都是抽象的概念，绘画的终极的缓冲区到底是如何管理的？缓冲区到底在哪里？
我们还是看看做终极的，最本质的设计概念，在从这些概念出发，来探讨这些概念的形成过程，是否有必要去生成写概念。SurfaceFlinger本质上干什么的？SurfaceFlinger的确就是这个意义：应用程序通过SurfaceFlinger将自己的“Surface”投掷到屏幕缓冲区。