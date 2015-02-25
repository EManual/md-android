好了，我不知道大家看完这个例子有没有什么不明白的地方？或者哪里有疑问？为什么要这样？ 我就说说我的疑问吧，我看完这个例子就有很多问题。大家都注意到了 我们的testView里边有一个initBitmap方法 还有这么一句。
```  
canvas.drawText(mstrTitle, 0, 100, p); 
```
这个方法里边的代码，请大家一定要仔细看看，我看完之后的疑问就是我们在initBitmap方法里边不是都调用。
```  
canvas.drawText(mstrTitle, 0, 100, p); 
```
把文字画到屏幕上了吗为什么还要到onDraw里边在指定位图再画一次？刚开始晕的不行，我不知道这是为什么，在这里我就不贴代码了，其实大家可以试试的比如我自己定义一方法我把所有的画图操作都放到我自己定义的方法里边来，我在自己new一个Canvas对象并且调用它的drawBitmap 绘制位图 试试看。我就不用onDraw方法来画图。我也不用onDraw方法提供的 canvas对象。这里我就不演示了 大家 尽管自己疯狂的试试吧。 我想结果肯定是 不是异常，就是图画不出来前提是你得用自己new一个Canvas对象。就是你得用自己new 的Canvas对象去调用drawBitmap方法。 
那我为什么用自己定义的Canvas对象就不能画出图来呢？不知道我的意思表达清楚没有，这里我觉得一定得多试试了不然没体会的。其实大家可以多看看不管是我博客的例子还是网上的例子或者书上的例子，其实我也才学android不久你就会发现所有的画图不管是简单的花一些几何图像，长方形，圆形啊。还是一些图片的旋转缩放操作啊，我们应该注意到它们用的都是Draw方法提供的那个Canvas对象。像上边mo-android的那个例子它虽然new了一个Canvas但是最后还得去onDraw方法里边用onDraw方法提供的那个canvas对象调用drawBitmap方法来显示位图而这个位图恰恰是与刚才new 的那个Canvas对象关联的。这里先给大家看几个方法吧，就是觉得应该知道或者得注意一下。 
```  
public Canvas(Bitmap bitmap) 
```
构建一个具有指定位图绘制到画布上。位图必须是可变的。在画布最初的密度是与给定的位图的密度相同，这就是Canvas类的一个构造方法没什么，这里提示一下它需要的是一个 可变的位图。 下一个 
```  
Public void setBitmap(Bitmap bitmap) 
```
此方法说明：指定一个可变的位图绘制到画布，画图的密度匹配位图，这也是Canvas的一个方法 也是需要一个可变的位图。 
```  
public final boolean isMutable() 
```
Bitmap有这样一个方法来判断位图是否可变 如果可变返回值为true 
```  
Bitmap bitmap = Bitmap.createBitmap(160, 250, Config.ARGB_8888);
```
createBitmap是Bitmap类的一个静态方法 它返回的也是一个 可变的位图。我为什么这样说呢 ？
效果图：
![img](P)  
Bitmap的源码里有这样一个变量
```  
private final boolean mIsMutable; 
```
图中的mIsMutable  的值 为true 刚才上边已经说了Bitmap有一个isMutable方法可以判断一个位图是否为可变 值为true说明是可变的。 
```  
private Bitmap mBitmap; 
private GL mGL; 
```
在android有这样一个概念就是native canvas这里就不翻译了。(比如什么母画布或者本地画布)我们的native canvas可以是屏面或者是GL或者图片画布。如果是屏面，我们的GL对象 mGL将是null, mBitmap可能会也可能不会。
目前(我们的默认构造方法创建一个画布，但是这个画布没有屏面) 也就是说如果你这样创建一个画布
Canvas canvas = new Canvas(); 这时候这个canvas对象将没有屏面也没有java-bitmap可以理解为java的位图。如果我们是以Gl为基础(native canvas)，然后mBitmap将是空的，mGl不能为空。因此这2个对象不可能都是非空的，因为这2个对象只要有一个不为空，另外的一个就得为空。但是有可能两个都为空。
![img](P)  
最后我们来看看代码：
```  
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.graphics.Bitmap.Config;
import android.graphics.drawable.BitmapDrawable;
import android.view.KeyEvent;
import android.view.MotionEvent;
import android.view.View;
public class GameView extends View implements Runnable {
	/* 声明Bitmap对象 */
	Bitmap mBitQQ = null;
	Paint mPaint = null;
	/* 创建一个缓冲区 */
	Bitmap mSCBitmap = null;
	/* 创建Canvas对象 */
	Canvas mCanvas = null;
	public GameView(Context context) {
		super(context);
		/* 装载资源 */
		mBitQQ = ((BitmapDrawable) getResources().getDrawable(R.drawable.qq)).getBitmap();
		/* 创建屏幕大小的缓冲区 */
		mSCBitmap = Bitmap.createBitmap(320, 480, Config.ARGB_8888);
		/* 创建Canvas */
		mCanvas = new Canvas();
		/* 设置将内容绘制在mSCBitmap上 */
		mCanvas.setBitmap(mSCBitmap);
		mPaint = new Paint();
		/* 将mBitQQ绘制到mSCBitmap上 */
		mCanvas.drawBitmap(mBitQQ, 0, 0, mPaint);
		/* 开启线程 */
		new Thread(this).start();
	}
	public void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		/* 将mSCBitmap显示到屏幕上 */
		canvas.drawBitmap(mSCBitmap, 0, 0, mPaint);
	}
	// 触笔事件
	public boolean onTouchEvent(MotionEvent event) {
		return true;
	}
	// 按键按下事件
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		return true;
	}
	// 按键弹起事件
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		return false;
	}
	public boolean onKeyMultiple(int keyCode, int repeatCount, KeyEvent event) {
		return true;
	}
	[Tags]/**
	 [Tags]* 线程处理
	 [Tags]*/
	public void run() {
		while (!Thread.currentThread().isInterrupted()) {
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				Thread.currentThread().interrupt();
			}
			// 使用postInvalidate可以直接在线程中更新界面
			postInvalidate();
		}
	}
}
```