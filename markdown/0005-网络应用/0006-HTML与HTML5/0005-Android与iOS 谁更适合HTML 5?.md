科技网站Fiercedeveloper近日刊载了一篇探讨iOS与Android平台性能的文章，将Android4和iOS5做了一番比较。
2011年12月上旬，Google发布了Android移动操作系统的最新重大升级.新操作系统是Android 4，代号为“冰淇淋三明治（Ice Cream Sandwich）”。对于Web开发人员而言，这是对Android的一次大考：如果要在iOS和Android上构建跨浏览器的应用，HTML 5是可行的解决方案吗？我们在Sencha测试了最新版本的iOS 5和Android 4，以了解每个平台提供给Web开发人员的特性以及它们各自的优势。
为了成为一流的Web应用平台，浏览器要向Web开发人员提供一系列核心功能：渲染引擎，用于尽可能流畅地显示视觉元素；Javascript引擎，用于执行应用程序逻辑；以及DOM（文档对象模型）和浏览器API，用于提供HTML5的特性以及支持发起网络请求、上传文件、操作页面等动作.为了从Web应用开发人员的角度比较Android 4和iOS 5，我们分别讨论了这三部分内容。
#### WebKit：Android 4进步明显，但仍然落后。
几乎所有移动设备的Web浏览器都使用了WebKit渲染引擎.WebKit最先起源于苹果的开源项目KDE/KHTML，现在Google、Qualcomm、RIM和其他很多厂商都加入了WebKit家族.它现在已经成为移动设备上渲染Web内容的事实标准.Android 4和iOS 5浏览器都基于WebKit，但是版本稍有不同.Android 4实现了WebKit 534.30，而苹果则实现了534.46。
虽然iOS的版本较新，但它们之间的差距很小，这是因为WebKit的渲染性能取决于它在硬件和软件上的具体实现.我们在测试中发现Android 4的渲染速度比Android 2.x和Android 3有明显提高.触摸滚动变得顺畅了很多，Android上常见的停顿也基本上完全消失了.不幸的是，它在渲染上有明显的缺陷，比如在使用JavaScript和CSS3移动屏幕上的元素时会出现闪烁和滚屏缓慢.对于依赖动态地移动元素的Web应用来说，Android 4的表现比Android 2.2要差.总体上说，Google在增强浏览器体验方面取得了很大的进步。
同时，Android 4新支持了很多CSS3特性，而iOS 5很早之前就支持这些特性.具体说来，Android 4现在完善地支持了CSS3 2D和3D变换、动画、过渡和反射.这对于Android来说是巨大的进步，因为开发人员在设计流畅而漂亮的Web应用时不会再只想到苹果。伴随着对这些特性的支持，我们希望Google和Android硬件供应商一起努力，对其产品仔细琢磨，实现无闪烁和高性能，以获得开发人员对高级渲染特性的真正支持。
#### JavaScript：性能旗鼓相当
在iOS 5中，苹果引入了新的JavaScript引擎Nitro，它在移动Safari浏览器中能极大地提高JavaScript的性能.一段时间内，iOS 5的移动浏览器JavaScript引擎是业内最快的.Android再次迎头赶上：Android 4中的JavaScript引擎比起Android 2.x（Gingerbread）有了很明显的提升.在某些硬件上，它比iOS 5更快.为了实现这一目标，Google引入了之前Chrome浏览器的JavaScript V8引擎，最终使得JavaScript的执行速度提升了2倍多.现在iOS 5和Android 4在JavaScript方面基本完全一样，这意味着开发人员应该假设在这两种平台上开发基本没有差别。
#### iOS 5/Safari在API上略胜一筹
浏览器之争的最后一部分则是浏览器API，它包括网络访问、文件系统访问、Canvas和其他富应用程序所需的功能.iOS对API的支持一贯领先.iOS 5支持某些特性，比如“overflow： scroll”；WebKit私有的属性“-webkit-overflow-scrolling： touch”（允许独立的滚动区域和触摸回弹）；Web Sockets（用于即时通信）；Web Workers（用于后台处理）；大量的其他HTML 5输入类型（比如数字和日期）.Android 4不支持这些常见的HTML 5特性，但是也有一个突出的亮点：对文件API的支持.文件API让开发人员能够操作设备上的本地文件，能够开发更富体验的应用，同时还能访问手机摄像头旋转等功能.尽管如此，iOS 5还是提供了更丰富的浏览器API，对HTML 5特性有更广泛的支持。
#### 现在iOS 5全面领跑
移动浏览器的领袖还是iOS 5.尽管Android在JavaScript的性能上已经和苹果并驾齐驱，但是总体说来，移动平台的Safari支持的API更多，图形性能更好.Android 4的浏览器取得了很大的进步，获得了更好的视觉效果（但是有缺陷）和渲染速度，更快的JavaScript引擎。Android 4比以前的任何版本都要好.正在寻求Web标准以提供跨平台解决方案的开发人员在使用HTML 5时会比以前更舒服，因为Android 4中的改进标志着Google的迅猛发力，这为他们的应用开启了巨大的潜在市场。