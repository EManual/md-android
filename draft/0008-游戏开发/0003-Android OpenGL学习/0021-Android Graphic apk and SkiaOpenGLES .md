Android apk里面的画图分为2D和3D两种：2D是由Skia来实现的，也就是我们在框架图上看到的SGL，SGL也会调用部分opengl的内容来实现简单的3D效果；3D部分是由OpenGL|ES实现的，OpenGL|ES是Opengl的嵌入式版本，我们先了解一下Android apk的几种画图方式，然后再来来看一看这一整套的图形体系是怎么建立的。
首先画图都是针对提供给应用程序的一块内存填充数据，没去研究过一个Activity是否就对应着底层的一个surface，但是应该都是对这块surface内存进行操作。因此说穿了就是我们要么调用2D的API画图，要么调用3D的API画图，然后将画下来的图保存在这个内存中，最后这个内存里面的内容会被Opengl渲染以后变为可以在屏幕上的像素信息。
一 、Apk应用主要关心的还是这些API的使用，在Android apk里面画图有2种方式 [2D]：
1、Simple Graphics in View
就是直接使用Android已经实现的一些画图操作，比如说images，shapes，colors，pre-defined animation等等，这些简单的画图操作实际上是由skia来提供的2D图形操作。使用这些预定义好的操作，我们可以实现诸如贴一张背景图啊，画出简单地形状阿，实现一些简单的动画之类的操作。这里的简单可以这么理解，就是我们在这里没有一笔一画地构造出一个图形出来，我们只是把我们的Graphic资源放入View体系中，由系统来将这些Graphic画出来。举个例子：我们现在在Activity里面绑定一个ImageView，我们可以设置这 个ImageView的内容是我们的picture，然后我们可以让这个picture整体颜色上来点蓝色调，然后我们还可以为这个ImageView加入一个预定义动画，这样当系统要显示这个View的时候就会显示我们的picture，并且会有动画，并带有一个蓝色调，我们并没有自己去定义画图操作，而是将这些内容放入View中，由系统来将这些内容画出来。这种方式只能画静态或者极为简单的2D图画，对于实时性很强的动画，高品质的游戏都是没法实现的。
2、Canvas
首先我们要明白这个Canvas是一个2D的概念，是在Skia中定义的。也就是说在这个方式下还是说的画2D图形。我们可以把这个Canvas理解成系统提供给我们的一块内存区域(但实际上它只是一套画图的API，真正的内存是下面的Bitmap)，而且它还提供了一整套对这个内存区域进行操作的方法，所有的这些操作都是画图API。也就是说在这种方式下我们已经能一笔一划或者使用Graphic来画我们所需要的东西了，要画什么要显示什么都由我们自己控制。这种方式根据环境还分为两种：一种就是使用普通View的canvas画图，还有一种就是使用专门的SurfaceView的canvas来画图。 两种的主要是区别就是可以在SurfaceView中定义一个专门的线程来完成画图工作，应用程序不需要等待View的刷图，提高性能。前面一种适合处理量比较小，帧率比较小的动画，比如说象棋游戏之类的；而后一种主要用在游戏，高品质动画方面的画图。
下面是这两种方式的典型sequence ：
2.1、View canvas
(1)  定义一个自己的View ：class your_view extends View{}  ；
(2)  重载View的onDraw方法：protected void onDraw(Canvas canvas){} ；
(3)  在onDraw方法中定义你自己的画图操作 ；
ps: 可以定义自己的Btimap：
```  
Bitmap b = Bitmap.createBitmap(100, 100, Bitmap.Config.ARGB_8888);
Canvas c = new Canvas(b);
```
但是必须将这个Bitmap放入View的canvas中，画的图才能显示出来：public void drawBitmap (Bitmap bitmap, Matrix matrix, Paint paint)；
invalidate()这个函数被调用后，系统会在不久的将来重新调用onDraw()函数，这个时间很短但并不是可预知的，我曾经在onDraw()中写了一个if语句，利用validate让系统死循环调用onDraw刷两张图，几乎上这两张图是刷在一起的。
2.2、Surface View Canvas
(1)  定义一个自己的SurfaceView : class your_surfaceview extends SurfaceView  implements SurfaceHolder.Callback() {}  ;
(2)  实现SurfaceHolder.Callback的3个方法surfaceCreated()surfaceChanged()surfaceDestroyed() ;
(3)  定义自己的专注于画图的线程  : class your_thread extends Thread() {} ;
(4)  重载线程的run()函数  ［一般在run中定义画图操作，在surfaceCreated中启动这个线程］
(5)  画图的过程一般是这样的：
```  
SurfaceHolder surfaceHolder = getHolder() ;//取得holder，这个holder主要是对surface操作的适配，用户不具备对surface操作的权限
surfaceHolder.addCallback(this) ; //注册实现好的callback
Canvas canvas ＝ surfaceHolder.lockCanvas() ; //取得画图的Canvas
/*---------------------------------画图
 * *-------------------------------- 画图结束 */
surfaceHolder.unlockCanvasAndPost() ; //提交并显示
```
所有前面3种方式的画图的一些例子在SDK上都有，写得也比较清楚，我这里就不说了，这里写一下我调这些代码过程一些小经验，应该主要涉及的是Activity这方面，应该以后都用得到：
首先是关于Eclipse的 ：
(1) ctrl ＋ shift ＋ O 可以自动添加需要的依赖包，这功能挺实用的觉得，还有alt + /是语法补全；
(2) 代码中右键比较实用的功能有很多，我记得的是F3找类的声明，F4找类的继承关系 ；
(3) 断点调试比较方便的，在Eclipse的右上交可以选择阅读代码的方式，还能经如debug模式，我现在用到的两个打log的方式：
```  
Log.e("class", "value : "+ classname) ; //检测class是否为空指针
Log.e(this.getClass().getName(), "notice message");
```
然后是关于Activity的：
(1) 首先尽量把UI的设计放在XML中实现，而不要放在代码中实现，这样方便设计，修改和移植；
(2) 所有使用到的component都必须在manifest中声明，不然程序中找不到相应的conponet的时候会报错；
(3) 一般每一个Activity都对应于一个类，和一个相应的布局文件xml ；
(4) 每一个Activity只有使用setContentView()绑定内容后才会显示，而且你才能从这个内容(比如xml中)获取到你需要的元素；
(5) res/drawable和res/raw中的元素的区别是drawable中的元素像素可能会被系统优化，而raw中的不会被优化；
(6)当多个Activity都从res/drawable中获得同一个元素，如果其中一个修改它的属性，所有其他的Activity中这个元素的相应属性都会改变；
(7) res/anim中保存的是动画相关的xml；
下面我们总结以下2D画图用到的包：
```  
android.view //画图是在View中进行的
android.view.animation //定义了一些简单的动画效果Tween Animation 和 Frame. Animation
android.graphics//定义了画图比较通用的API，比如canvas，paint，bitmap等
android.graphics.drawable  //定义了相应的Drawable(可画的东西)，比如说BitmapDrawable，PictureDrawable等
android.graphics.drawable.shapes//定义了一些shape
```
二、了解了2D，我们再来看看3D的画图方式。3D画图SDK上讲得很简单，只是提了一个通用的方式，就是继承一个View，然后在这个View里面获得 Opengl的句柄进行画图，道理应该来说是和2D一样的，差别就是一个是使用2D的API画图，一个是使用3D的。不过因为3D openGl|ES具有一套本身的运行机制，比如渲染的过程控制等，因此Android为我们提供了一个专门的用在3D画图上的GLSurfaceView。这个类被放在一个单独的包android.opengl里面，其中实现了其他View所不具备的操作：
(1) 具有OpenGL|ES调用过程中的错误跟踪，检查工具，这样就方便了Opengl编程过程的debug ；
(2) 所有的画图是在一个专门的Surface上进行，这个Surface可以最后被组合到android的View体系中 ；
(3) 它可以根据EGL的配置来选择自己的buffer类型，比如RGB565，depth＝16
 (这里有点疑问，SurfaceHolder的类型是SURFACE_TYPE_GPU，内存就是从EGL分配过来的？)
(4) 所有画图的操作都通过render来提供，而且render对Opengl的调用是在一个单独的线程中
(5) Opengl的运行周期与Activity的生命周期可以协调
下面我们再看看利用GLSurface画3D图形的一个典型的Sequence
(1)  选择你的EGL配置(就是你画图需要的buffer类型)[optional]：
```  
setEGLConfigChooser(boolean)
setEGLConfigChooser(EGLConfigChooser) 
setEGLConfigChooser(int, int, int, int, int, int)
```
(2) 选择是否需要Debug信息 [optional]:
```  
setDebugFlags(int)
setGLWrapper(GLSurfaceView.GLWrapper)
```
(3) 为GLSurfaceView注册一个画图的renderer ： setRenderer(GLSurfaceView.Renderer)
(4) 设置reander mode，可以为持续渲染或者根据命令 来渲染,默认是continuous rendering [optional]： setRenderMode(int)
这里有一个要注意的地方就是必须将Opengl的运行和Activity的生命周期绑定在一起，也就是说Activity pause的时候，opengl的渲染也必须pause。另外GLSurfaceView还提供了一个非常实用的线程间交互的函数queueEvent(Runnable)，可以用在主线程和render线程之间的交互，下面就是SDK提供的范例：
```  
class MyGLSurfaceView extends GLSurfaceView {
	private MyRenderer mMyRenderer;
	public void start() {
		mMyRenderer = ...;
		setRenderer(mMyRenderer);
	}
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		if (keyCode == KeyEvent.KEYCODE_DPAD_CENTER) {
			queueEvent(new Runnable() {
				// This method will be called on the rendering
				// thread:
				public void run() {
					mMyRenderer.handleDpadCenter();
				}
			});
			return true;
		}
		return super.onKeyDown(keyCode, event);
	}
}
```
GLSurfaceView是Android提供的一个非常值得学习 的类，它实际上是一个如何在View中添加画图线程的例子，如何在Java 中使用线程的例子，如何添加事件队列的例子，一个使用SurfaceView画图的经典Sequence，一个如何定义Debug信息的例子，觉得把它看懂了可以学到很多知识 ，具体的源码在：/framworks/base/opengl/java/android/opengl/GLSurfaceView.java 。
3D的内容基本到这里基本讲完了，剩下的主要是如何使用Opengl API的问题了，可以看看API demo中简单的立方体，复杂的可以看看它那个魔方的实现。下面我们总结一下3D画图需要用到的包：
```  
Android.opengl//主要定义了GLSurfaceView
javax.microedition.khronos.egl//java层的egl接口
javax.microedition.khronos.opengles  //opengl　API
```
三、了解了2D和3D基本的画图方法，我们再回过头来看看整个Android对Opengl和Ｓkia的调用层次关系
3.1、首先来看2D，2D是主要使用的图形引擎，毕竟3D受制于其过高的硬件要求在手机上使用还是比较少，而且Skia也能部分实现类似于3D的效果， 因此可以说SKia实现了Android平台上绝大多数的图形工作。下面我们来看看从应用层到底层对skia的调用关系： 
Android对skia的调用是一个比较经典 的调用过程，应用程序的几个包是在SDK中提供的；JNI放在框架的JNI目录下面的Graphic目录；skia是作为一个第三方组件放在external目录下面。我们可以稍微了解一下skia的结构： 
这里主要涉及到的3个库：
```  
libcorecg.so  包含/skia/src/core的部分内容，比如其中的Region，Rect是在SurfaceFlinger里面计算可是区域的操作基本单位
libsgl.so包含/skia/src/core|effects|images|ports|utils的部分和全部内容，这个实现了skia大部分的图形效果，以及图形格式的编解码
libskiagl.so包含/skia/src/gl里面的内容，主要用来调用opengl实现部分效果
```
另外我看到/skia/src中有两个目录animator和view没有写入makefile的编译路径中，我觉得这两个目录是很重要的，不知道是现在Android还没使用到，还是用其他的方式加载进去的。
要想在底层使用skia的接口来画图需要全面了解skia的一整套机制，实际上skia开源到现在还没多久，在网上能找到的资料是也是很粗浅的，如果将来真需要在这方面下功夫肯定是需要一定的工作量的。
3.2、Android对3D的调用曾经让我迷惑了一段时间，因为在framewoks/base/core/jni这个目录一直没找到跟opengl相关的内容，后面去仔细看看opengl里面的内容才知道Android把opengl的本地实现，JNI，java接口都放在/frameworks/base/opengl下面了，而且它内部还带了一个工具可以生成JNI代码。
我们来看看opengl的目录结构：
```  
/include包含egl和gles所有的头文件 
/java/android/opengl这个目录包含的就是我们3D画图要使用到的GLSurfaceView
/java/com/google/android/gles_jni这个目录包含一些自动生成的文件
/java/javax/microedition/khronos/egl这就是应用层使用到的egl接口
/java/javax/microedition/khronos/opengl  这就是应用层使用到的opengl接口
/libagl  这个就是opengl主要的实现了
/libs  这里面包含两个库的实现，一个是libegl.so 还有一个是libGL|ES_CM.so
/tools  在我的理解这就是一个jni的生成工具
```
Opengl编程谁都知道是一个大工程，我觉得现在对3D的需求应该是很低的，很多效果我们使用skia也可以实现。所以我觉得将来的重点应该还是放在skia上面。