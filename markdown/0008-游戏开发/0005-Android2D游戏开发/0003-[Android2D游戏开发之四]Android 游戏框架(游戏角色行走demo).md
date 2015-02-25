其实上一篇分析surfaceview的文章就是一个简单的游戏框架了，当然这里再强调一下，简单的游戏框架，所以不要高手们不要乱喷~
这个Demo是给群里一童鞋写的一个对图片操作以及按键处理，游戏简单框架的一个demo，这里放出给大家分享~
```     
import android.content.Context;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.util.Log;
import android.view.KeyEvent;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.SurfaceHolder.Callback;
public class MySurfaceView extends SurfaceView implements Callback, Runnable {
	private Thread th = new Thread(this);
	private SurfaceHolder sfh;
	private int SH, SW;
	private Canvas canvas;
	private Paint p;
	private Paint p2;
	private Resources res;
	private Bitmap bmp;
	private int bmp_x = 100, bmp_y = 100;
	private boolean UP, DOWN, LEFT, RIGHT;
	private int animation_up[] = { 3, 4, 5 };
	private int animation_down[] = { 0, 1, 2 };
	private int animation_left[] = { 6, 7, 8 };
	private int animation_right[] = { 9, 10, 11 };
	private int animation_init[] = animation_down;
	private int frame_count;
	public MySurfaceView(Context context) {
		super(context);
		this.setKeepScreenOn(true);
		res = this.getResources();
		bmp = BitmapFactory.decodeResource(res, R.drawable.enemy1);
		sfh = this.getHolder();
		sfh.addCallback(this);
		p = new Paint();
		p.setColor(Color.YELLOW);
		p2 = new Paint();
		p2.setColor(Color.RED);
		p.setAntiAlias(true);
		setFocusable(true); // 备注1
	}
	public void surfaceCreated(SurfaceHolder holder) {
		SH = this.getHeight();
		SW = this.getWidth();
		th.start();
	}
	public void draw() {
		canvas = sfh.lockCanvas();
		canvas.drawRect(0, 0, SW, SH, p); // 备注2
		canvas.save(); // 备注3
		canvas.drawText("Himi", bmp_x - 2, bmp_y - 10, p2);
		canvas.clipRect(bmp_x, bmp_y, bmp_x + bmp.getWidth() / 13,
				bmp_y + bmp.getHeight());
		if (animation_init == animation_up) {
			canvas.drawBitmap(bmp,
					bmp_x - animation_up[frame_count] * (bmp.getWidth() / 13),
					bmp_y, p);
		} else if (animation_init == animation_down) {
			canvas.drawBitmap(
					bmp,
					bmp_x - animation_down[frame_count] * (bmp.getWidth() / 13),
					bmp_y, p);
		} else if (animation_init == animation_left) {
			canvas.drawBitmap(
					bmp,
					bmp_x - animation_left[frame_count] * (bmp.getWidth() / 13),
					bmp_y, p);
		} else if (animation_init == animation_right) {
			canvas.drawBitmap(bmp,
					bmp_x - animation_right[frame_count]
							[Tags]* (bmp.getWidth() / 13), bmp_y, p);
		}
		canvas.restore(); // 备注3
		sfh.unlockCanvasAndPost(canvas);
	}
	public void cycle() {
		if (DOWN) {
			bmp_y += 5;
		} else if (UP) {
			bmp_y -= 5;
		} else if (LEFT) {
			bmp_x -= 5;
		} else if (RIGHT) {
			bmp_x += 5;
		}
		if (DOWN || UP || LEFT || RIGHT) {
			if (frame_count < 2) {
				frame_count++;
			} else {
				frame_count = 0;
			}
		}
		if (DOWN == false && UP == false && LEFT == false && RIGHT == false) {
			frame_count = 0;
		}
	}
	@Override
	public boolean onKeyDown(int key, KeyEvent event) {
		if (key == KeyEvent.KEYCODE_DPAD_UP) {
			if (UP == false) {
				animation_init = animation_up;
			}
			UP = true;
		} else if (key == KeyEvent.KEYCODE_DPAD_DOWN) {
			if (DOWN == false) {
				animation_init = animation_down;
			}
			DOWN = true;
		} else if (key == KeyEvent.KEYCODE_DPAD_LEFT) {
			if (LEFT == false) {
				animation_init = animation_left;
			}
			LEFT = true;
		} else if (key == KeyEvent.KEYCODE_DPAD_RIGHT) {
			if (RIGHT == false) {
				animation_init = animation_right;
			}
			RIGHT = true;
		}
		return super.onKeyDown(key, event);
	}
	@Override
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		if (DOWN) {
			DOWN = false;
		} else if (UP) {
			UP = false;
		} else if (LEFT) {
			LEFT = false;
		} else if (RIGHT) {
			RIGHT = false;
		}
		return super.onKeyUp(keyCode, event);
	}

	@Override
	public void run() {
		while (true) {
			draw();
			cycle();
			try {
				Thread.sleep(100);
			} catch (Exception ex) {
			}
		}
	}

	@Override
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}

	@Override
	public void surfaceDestroyed(SurfaceHolder holder) {
	}
}
```
#### 备注1           此方法是用来响应按键！如果是自己定义一个继承自View的类，重新实现onKeyDown方法后，只有当该View获得焦点时才会调用onKeyDown方法，Actvity中的onKeyDown方法是当所有控件均没有处理该按键事件时，才会调用。
#### 备注2
这里也是对屏幕进行刷屏操作，其实这也只是一种，之前文章里我也用到drawRGB的方法同样实现，当然也可以用fillRect等来刷屏。    那么这里我想说下，在继承view中，因为onDraw方法是系统自动调用的，不像在surfaceview这里这样去在run里面自己去不断调用，在view中我们可以抵用 invalidate()/postInvalidate() 这两种方法实现让系统调用onDraw方法，这里也是和surfaceview中的不同之一！
#### 备注3
这里canvas.save();和canvas.restore();是两个相互匹配出现的，作用是用来保存画布的状态和取出保存的状态的。这里稍微解释一下，   当我们对画布进行旋转，缩放，平移等操作的时候其实我们是想对特定的元素进行操作，比如图片，一个矩形等，但是当你用canvas的方法来进行这些操作的时候，其实是对整个画布进行了操作，那么之后在画布上的元素都会受到影响，所以我们在操作之前调用canvas.save()来保存画布当前的状态，当操作之后取出之前保存过的状态，这样就不会对其他的元素进行影响。