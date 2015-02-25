大家平时见到的最多的可能就是Frame动画了，Android中当然也少不了它。它的使用更加简单，只需要创建一个AnimationDrawabledF对象来表示Frame动画，然后通过addFrame方法把每一帧要显示的内容添加进去，最后通过start方法就可以播放这个动画了，同时还可以通过setOneShot方法设置是否重复播放。 下面就是一个用Frame动画模拟日全食的效果。先看看效果。
![img](P)  
![img](P)  
![img](P)  
![img](P)  
Activity01
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.KeyEvent;
import android.view.Window;
import android.view.WindowManager;
public class Activity01 extends Activity {
	private GameView mGameView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 设置无标题栏
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		// 设置为全屏模式
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
		WindowManager.LayoutParams.FLAG_FULLSCREEN);
		mGameView = new GameView(this);
		setContentView(mGameView);
	}
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		if (mGameView == null) {
			return false;
		}
		mGameView.onKeyUp(keyCode, event);
		return true;
	}
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		if (mGameView == null) {
			return false;
		}
		if (keyCode == KeyEvent.KEYCODE_BACK) {
			// 关闭Activity
			this.finish();
			return true;
		}
		return super.onKeyDown(keyCode, event);
	}
}
```
GameView
```  
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.drawable.AnimationDrawable;
import android.graphics.drawable.Drawable;
import android.view.KeyEvent;
import android.view.View;
public class GameView extends View {
	// 定义AnimationDrawable动画
	private AnimationDrawable frameAnimation = null;
	Context mContext = null;
	// 定义一个Drawable对象
	Drawable mBitAnimation = null;
	public GameView(Context context) {
		super(context);
		mContext = context;
		// 实例化AnimationDrawable对象
		frameAnimation = new AnimationDrawable();
		/* 装载资源 */
		// 这里用一个循环装载所有名字类似的资源
		// 如"a1...........15.png"的图片
		for (int i = 1; i <= 15; i++) {
			int id = getResources().getIdentifier("a" + i, "drawable",
					mContext.getPackageName());
			// 此方法返回一个可绘制的对象与特定的资源ID相关联
			mBitAnimation = getResources().getDrawable(id);
			/* 为动画添加一帧 */
			// 参数mBitAnimation是该帧的图片
			// 参数500是该帧显示的时间，按毫秒计算
			frameAnimation.addFrame(mBitAnimation, 500);
		}
		/*
		 [Tags]* 上边用到了Resources的getIdentifier方法 方法返回一个资源的唯一标识符，如果没有这个资源就返回0
		 [Tags]* 0不是有效的标识符，在说说这个方法几个参数的含义
		 [Tags]* 第一个 就是我们的资源名称了。
		 [Tags]* 第二个 就是我们要去哪里找我们的资源 我们的图片在drawable 下 所以为drawable
		 [Tags]* 第三个 我们用了Context的getPackageName返回应用程序的包名
		 [Tags]*/
		// 设置播放模式是否循环播放，false表示循环，true表示不循环
		frameAnimation.setOneShot(false);
		// 设置本类将要显示的这个动画
		this.setBackgroundDrawable(frameAnimation);
	}
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
	}
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		switch (keyCode) {
		case KeyEvent.KEYCODE_DPAD_UP:
			// 当按手机的上方向键时开始播放frameAnimation.start();
			break;
		}
		return true;
	}
}
```