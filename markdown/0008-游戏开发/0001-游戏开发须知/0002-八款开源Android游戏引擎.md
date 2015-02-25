很多初学android游戏开发的朋友，往往会显得有些无所适从，他们常常不知道该从何处入手，每当遇到自己无法解决的难题时，又往往会一边羡慕于iPhone下有诸如Cocos2d-iphone之类的免费游戏引擎可供使用，一边自暴自弃的抱怨Android平台游戏开发难度太高，又连个像样的游戏引擎也没有，甚至误以为使用Java语言开发游戏是一件费力不讨好且没有出路的事情。
事实上，这种想法完全是没有必要且不符合实际的，作为能和苹果iOS分庭抗礼的Android（各种意义上），当然也会有相当数量的游戏引擎存在。仅仅因为我们处于这个狭小的天地间，与外界接触不够，所以对它们的存在茫然不知罢了。
下面我就罗列出八款常见的Android游戏引擎，以供有需要者参考（收费，下载量过小，不公布源码，以及鄙人不知道（-_-）的引擎不在此列）。
#### 1、Angle 
Angle是一款专为Android平台设计的，敏捷且适合快速开发的2D游戏引擎，基于OpenGL ES技术开发。该引擎全部用Java代码编写，并且可以根据自己的需要替换里面的实现，缺陷在于文档不足，而且下载的代码中仅仅包含有少量的示例教程。
最低运行环境要求不详。
项目地址：http://code.google.com/p/angle/ 
#### 2、Rokon 
rokon是一款Android 2D游戏引擎，基于OpenGL ES技术开发，物理引擎为Box2D，因此能够实现一些较为复杂的物理效果，该项目最新版本为 2.0.3 (09/07/10)。总体来说，此引擎最大的优点在于其开发文档相当之完备，并且项目作者对反馈Bug的修正非常之神速，所以该框架的使用在目前也最为 广泛，有人干脆将它称为Cocos2d-iPhone引擎的Android版（业务逻辑和编码风格上也确实很像）。附带一提，国内某个需要注册会员才能下 载的Android游戏框架衍生于此框架，所以大家也不要刻板的认为收费便一定是好的，免费就一定不好。
最低运行环境要求为Android 1.5。
项目地址：http://code.google.com/p/rokon/ 
#### 3、LGame 
LGame是一款国人开发的Java游戏引擎，有Android及PC(J2SE)两个开发版本，目前最高版本同为0.2.6(31/07/10)。其底层绘图器LGrpaphics封装有J2SE以及J2ME提供的全部Graphics API（PC版采用Graphics2D封装，Android版采用Canvas模拟实现），所以能够将J2SE或J2ME开发经验直接套用其中，两版本间主要代码能够相互移植。Android版内置有Admob接口，可以不必配置XML直接硬编码Admob广告信息。
该引擎除了基本的音效、图形、物理、精灵等常用组件以外，也内置有Ioc、xml、http等常用Java组件的封装，代价是jar体积较为庞大，PC版已突破1.2MB，Android版有所简化也在500KB左右。此外，该引擎还内置有按照1:1实现的J2ME精灵类及相关组件，可以将绝大多数J2ME游戏平移到Android或PC版中。唯一遗憾的是，该项目作者是个极其懒惰的家伙，开发文档从去年说到今年依旧没有提供，只有游戏示例可供下载。
最低运行环境要求为Android 1.1。
项目地址：http://code.google.com/p/loon-simple/
#### 4、AndEngine 
andengine同样是一款基于OpenGL ES技术的Android游戏引擎，物理引擎同样为Box2D（标配|||）。该框架性能普通，文档缺乏，但示例较为丰富。
下载地址（未直接提供jar下载，源码可通过svn提取）：http://code.google.com/p/andengine/
最低运行环境要求不详。
项目地址：http://code.google.com/p/rokon/
#### 5、libgdx 
libgdx是一款基于OpenGL ES技术开发的Android游戏引擎，支持Android平台下的2D游戏开发，物理引擎采用Box2D实现。单就性能角度来说，堪称是一款非常强大的Android游戏引擎，但缺陷在于精灵类等相关组件在使用上不够简化，而且文档也较为匮乏。
最低运行环境要求不详。
项目地址：http://code.google.com/p/libgdx/ 
#### 6、jPCT 
jPCT是一款基于OpenGL技术开发的3D图形引擎(PC环境为标准OpenGL，Android为OpenGL ES)， 以Java语言为基础的，拥有功能强大的Java 3D解决方案。该引擎与LGame（此为2D游戏引擎）相类似，目前拥有PC(J2SE)以及Android两个开发版本。
jPCT的最大优势之一，就在于它惊人的向下兼容性。在PC环境中，jPCT甚至可以运行在JVM1.1环境之中，因为jPCT内部提供的图形渲染接口完全符合所有的Java1.1规范（就连已经消失的Microsoft VM乃至更古老的Netscape 4 VM也不例外）。
最低运行环境要求为Android 1.5。
项目地址：http://www.jpct.net/jpct-ae/
#### 7、Alien3d 
Alien3d是一款体积非常之小的Android 3D游戏引擎，基于OpenGL ES技术开发。为了压缩体积，它根据不同功能采用多jar方式发布（包括alien3d-engine.jar，alien3d-tiled.jar，alien3d-sprites.jar，alien3d-shapes.jar，alien3d- particles2d.jar，），事实上它的核心文件大约只有40KB，所有相关jar的总和也不足150KB。
最低运行环境要求为Android 1.5。
项目地址：http://code.google.com/p/alien3d/
#### 8、Catcake 
Catcake是一款跨平台的Java 3D图形引擎，目前支持PC(J2SE)及Android环境运行（已有iPhone版规划）。该引擎在易用性和运行性能上皆有出色的表现，支持常见的游戏开发功能，诸如精灵动画，音频处理和视频播放等。
最低运行环境要求为Android 1.6。
项目地址：http://code.google.com/p/catcake/