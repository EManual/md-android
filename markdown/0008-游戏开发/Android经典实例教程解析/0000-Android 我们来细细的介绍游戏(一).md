#### 1. 编程前需要认真考虑的问题
实现《是男人就坚持20秒》游戏编程之前，需要先考虑以下三个问题：
需要用到几个Activity；
需要用到几个线程；
是用View还是SurfaceView来实现画面绘制。
《是男人就坚持20秒》游戏比较简单，不需要使用高级组件，也不会涉及到底层开发。因此，不需要在各种功能Activity间切换，在主Activity绘制游戏画面就可以了。
UI线程必不可少，除此之外，需要一个游戏主线程。由于需要加载的资源较少，不需要开辟资源加载线程；游戏没有使用网络功能，也没有音乐效果。因此，也不需要开辟额外的线程。
游戏策划规定每秒钟实现30帧的游戏画面，即每1/30秒刷新一帧，用View实现的画面绘制不能严格恒帧，游戏画面的流畅性不能满足要求。因此，必须使用SurfaceView直接控制游戏界面绘制。
现在就可以回答之前提到的三个问题了：
需要用到几个Activity：一个Activity；
需要用到几个线程：两个线程；
用View还是surfaceView来实现画面绘制：SurfaceView。
Surface是Android图形系统中一个重要的概念，View及其子类（如TextView、Button）都得画在Surface上，Surface的内容直接对应屏幕上显示的内容。每个Surface创建一个Canvas对象（但属性时常改变），用来管理View在Surface上的绘图操作。
View和SurfaceView的区别在于：View是通过信息（message)机制间接指挥Surface完成绘制，SurfaceView则是直接操作底层接口，接管Surface的绘制。
#### 2. 游戏主线程
考虑清楚了上述的三个问题之后，就可以开始编写代码了。首先，用两个变量mMode和pMode来保存游戏状态，变量取值及对应关系最好能够写到一个文档中，以便查看，如表所示。
![img](P)  
申明一个线程，作为游戏主线程。
```  
class ManThread extends Thread {
	private SurfaceHolder mSurfaceHolder;
	private Context mContext;
	private Handler mHeadler;
	private Bitmap mBackgroundImage;
	// 背景
	private Bitmap pBitmap;
	// "飞机"的bitmap
	private Drawable p;
	// 飞机
	private Drawable bomb1, bomb2, bomb3, bomb4, bomb5, bomb6, bomb7, bomb8;
	// 爆炸图
	private Drawable paodan;
	// 炮弹
	private int mWidth, mHeight;
	// "飞机"的宽和高
	private int mCanvasWidth;
	// 屏幕宽
	private int mCanvasHeight;
	// 屏幕高
	private Paint paint, paint2;
	private int px;
	// 飞机位置→x坐标
	private int py;
	// 飞机位置→y坐标
	// 状态
	public static final int STATE_MENU = 1;
	public static final int STATE_OVER = 2;
	public static final int STATE_LOAD = 3;
	public static final int STATE_RUNNING = 5;
	public static final int STATE_PAUSE = 6;
	// 按键
	public static final int DIRECTION_UP = 1;
	public static final int DIRECTION_DOWN = 2;
	public static final int DIRECTION_RIGHT = 4;
	public static final int DIRECTION_LEFT = 8;
	public static final int STATE_RESUME = 16;
	public static final int STATE_RESTART = 32;
	public static final int STEP = 3;
	// 飞机每一帧的移动距离
	public static final long time_fps = 1000 / 30;
	// 每秒的帧数
	private long startTime;
	// 游戏开始时间
	private long endTime;
	// 结束/当前时间
	private long lastTime_fast;
	// 上一次生成快速炮弹的时间
	private long lastTime_slow;
	// 上一次生成慢速炮弹的时间
	private long pauseTime;
	// 暂停时间
	public int key_down;
	// 按键信息
	private int p_hurt;
	// 中弹信息。p_hurt>0表示飞机中弹
	private int offset;
	// 偏移量，用于飞机碰撞图层
	private int k1, k2;
	// 数组偏移变量，用于滚动数组
	private int end1, head1, end2;
	// 滚动数组的首和尾
}
```