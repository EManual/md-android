ManThread线程的构造函数如下：
```  
public ManThread(SurfaceHolder sh, Context context, Handler ha) {
	mSurfaceHolder = sh;
	mContext = context;
	mHandler = ha;
	// 进入游戏资源加载状态
	mMode = STATE_LOAD;
	pMode = 0;
	p_hurt = 0;
	// 飞机未中弹
	pauseTime = -1;
	// 游戏未暂停
}
```
重载游戏主线程中的run()方法，将之前设计好的游戏框架移植其中，代码如下：
```  
@Override
public void run() {
	long t1, t2;
	t1 = System.currentTimeMillis();
	// 前一次刷新时间
	while (mMode != STATE_OVER)
		// 游戏未结束 {
		t2 = System.currentTimeMillis();
	// 获取当前时间
	if (t2 - t1 < time_fps) {
		try {
			sleep(time_fps + t1 - t2);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	} else {
		t1 += time_fps;
		// 刷新时间
		State_Change();
		// 状态转移
		if (pauseTime != -1)
			continue;
		// 用于处理游戏暂停时的状态,可以先跳过
		Update_Fps();
		// 状态执行
		Canvas c = null;
		try {
			c = mSurfaceHolder.lockCanvas(null);
			// 开始对surfaceView进行编辑
			synchronized (mSurfaceHolder) {
				doDraw(c);
				// 通过canvas绘制surface
			}
		} finally {
			if (c != null) {
				mSurfaceHolder.unlockCanvasAndPost(c);
				// 释放对surfaceView 的编辑,并将surfaceView的内容显示到屏幕
			}
		}
	}
}
```
实现State_Change()、Update_Fps()、doDraw()这三个方法之前，先来看一下获取按键输入部分的代码。
```  
boolean doKeyDown(int key, KeyEvent msg) {
	if (key == KeyEvent.KEYCODE_DPAD_UP)
		key_down |= DIRECTION_UP;
	if (key == KeyEvent.KEYCODE_DPAD_DOWN)
		key_down |= DIRECTION_DOWN;
	if (key == KeyEvent.KEYCODE_DPAD_RIGHT)
		key_down |= DIRECTION_RIGHT;
	if (key == KeyEvent.KEYCODE_DPAD_LEFT)
		key_down |= DIRECTION_LEFT;
	if (key == KeyEvent.KEYCODE_DPAD_CENTER)
		key_down |= STATE_RESUME;
	return true;
}
boolean doKeyup(int key, KeyEvent msg) {
	if (key == KeyEvent.KEYCODE_DPAD_UP)
		key_down &= 63 - DIRECTION_UP;
	if (key == KeyEvent.KEYCODE_DPAD_DOWN)
		key_down &= 63 - DIRECTION_DOWN;
	if (key == KeyEvent.KEYCODE_DPAD_RIGHT)
		key_down &= 63 - DIRECTION_RIGHT;
	if (key == KeyEvent.KEYCODE_DPAD_LEFT)
		key_down &= 63 - DIRECTION_LEFT;
	if (key == KeyEvent.KEYCODE_DPAD_CENTER)
		key_down &= 63 - STATE_RESUME;
	return true;
}
```
代码解释：用变量key_down保存按键信息，key_down的每一位对应一个按键。当某一个按键被按下后，key_down的对应位将被置1；当该按键弹起时，key_down的对应位置0。