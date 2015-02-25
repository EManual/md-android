在读Android SDK里面的samples里面游戏的时候，很多时候都看到对canvas的save()和restore()运用。下面是个小程序，使用了这两个方面，使得旋转红色方块的时候，保证蓝色方块不受影响。
```  
import android.app.Activity;
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.os.Bundle;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
public class Test extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(new MyView(this));
	}
	class MyView extends SurfaceView implements SurfaceHolder.Callback {
		private SurfaceHolder mHolder;
		private Canvas canvas;
		public MyView(Context context) {
			super(context);
			mHolder = getHolder();
			mHolder.addCallback(this);
		}
		@Override
		public void surfaceChanged(SurfaceHolder holder, int format, int width,
				int height) {
		}
		@Override
		public void surfaceCreated(SurfaceHolder holder) {
			canvas = mHolder.lockCanvas();
			Paint mPaint = new Paint();
			mPaint.setColor(Color.BLUE);
			canvas.drawRect(100, 200, 200, 300, mPaint);
			canvas.save();
			canvas.rotate(45);
			mPaint.setColor(Color.RED);
			canvas.drawRect(150, 10, 200, 60, mPaint);
			canvas.restore();
			mHolder.unlockCanvasAndPost(canvas);
		}
		@Override
		public void surfaceDestroyed(SurfaceHolder holder) {
		}
	}
}
```
效果图：
![img](P)  