我们通过onDraw来绘制图，用invalidate的方法来实现界面的刷新，注意invalidate不能直接在进程中调用，因为它违背了单线程的模式：android的UI操作并不是线程安全的，并且这些操作必须在UI线程当中执行，因此android中最常用的方法是利用Handler来实现UI进程的更新
```  
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.KeyEvent;
public class Activity01 extends Activity {
	private static final int REFRESH = 0x000001;
	private GameSurfaceView mGameSurfaceView = null;
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.mGameSurfaceView = new GameSurfaceView(this);
		setContentView(mGameSurfaceView);
		// start the thread
		new Thread(new GameThread()).start();
	}
	Handler myHandler = new Handler() {
		public void handleMessage(Message msg) {
			switch (msg.what) {
			case Activity01.REFRESH:
				mGameSurfaceView.invalidate();
				break;
			}
			super.handleMessage(msg);
		}
	};
	class GameThread implements Runnable {
		public void run() {
			while (!Thread.currentThread().isInterrupted()) {
				Message message = new Message();
				message.what = Activity01.REFRESH;
				// send message
				Activity01.this.myHandler.sendMessage(message);
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {
					Thread.currentThread().interrupted();
				}
			}
		}
	}
	public boolean onKeyUp(int keyCode, KeyEvent event) {
		switch (keyCode) {
		case KeyEvent.KEYCODE_DPAD_UP:
			mGameSurfaceView.y -= 3;
			break;
		case KeyEvent.KEYCODE_DPAD_DOWN:
			mGameSurfaceView.y += 3;
			break;
		}
		return true;
	}
}
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
public class GameView extends View {
	int miCount = 0;
	int y = 0;
	public GameView(Context context) {
		super(context);
	}
	public void onDraw(Canvas canvas) {
		if (miCount < 100) {
			miCount++;
		} else {
			miCount = 0;
		}
		Paint mPaint = new Paint();
		switch (miCount % 4) {
		case 0:
			mPaint.setColor(Color.BLUE);
			break;
		case 1:
			mPaint.setColor(Color.GREEN);
			break;
		case 2:
			mPaint.setColor(Color.RED);
			break;
		case 3:
			mPaint.setColor(Color.YELLOW);
			break;
		default:
			mPaint.setColor(Color.WHITE);
			break;
		}
		// paint the rectangle
		canvas.drawRect((500 - 80) / 2, y, (500 - 80) / 2 + 80, y + 40, mPaint);
	}
}
```