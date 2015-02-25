我们今天就来用一个android游戏代码给大家讲一讲，android游戏是怎么样写出来的。我们大家还是先来看看代码吧：
```  
import android.app.Activity;
import android.content.Context;
import android.content.Intent;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Rect;
import android.graphics.Typeface;
import android.view.KeyEvent;
import android.view.SurfaceHolder;
import android.view.SurfaceHolder.Callback;
import android.view.SurfaceView;
import android.view.View;
public class MySurfaceView extends SurfaceView implements Callback, Runnable {
	private SurfaceHolder sfh;
	private Thread th;
	private Canvas canvas;
	private Paint paint;
	private int ScreenW, ScreenH;
	// ball informationetContentView
	private int moveSpeedX = 10;
	private int moveSpeedY = 10;
	private int tempSpeedX = moveSpeedX;
	private int tempSpeedY = moveSpeedY;
	private int head_left = 21;
	private int head_top = 41;
	private int radius = 10;// 半径
	private int ball_score = 100;
	// Bat information
	private int bat_left = 10;
	private int bat_right = 10;
	private int bat_width = 55;
	private int bat_ply = 5;
	private int bat_move_speed = 16;
	private int ball_color = Color.WHITE;
	// Process information
	private int refresh_circle_time = 50;
	// Game status
	private int score = 0;
	private int score_count = 0;
	private double time = 0.0;
	private boolean show_score = false;
	private int game_status = GameStatus.GAME_READY;
	private int chances = 0;
	// Window variabele
	int off_count = 1;
	private Activity main_activity;
	public MySurfaceView(Context context) {
		super(context);
		main_activity = (Activity) context;
		sfh = this.getHolder();
		sfh.addCallback(this);
		paint = new Paint();
		paint.setAntiAlias(true);
		paint.setColor(Color.WHITE);
		paint.setTextSize(16);
		this.setKeepScreenOn(true);// 保持屏幕常亮
		setFocusable(true);
		th = new Thread(this);
	}
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		// TODO Auto-generated method stub

		if (game_status == GameStatus.GAME_NEW) {
			if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
				game_status = GameStatus.GAME_READY;
				drawReadyWindow();
			}
		}
		if (game_status == GameStatus.GAME_OVER) {
			if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
				init_game_data();
				game_status = GameStatus.GAME_READY;
				drawReadyWindow();
			}
		}
		if (game_status == GameStatus.GAME_PAUSED) {
			// drawPausedWindow();
			/*
			 [Tags]* 显示快捷键菜单，并提示游戏已经ready
			 [Tags]*/
			if (keyCode == KeyEvent.KEYCODE_DPAD_RIGHT
					|| keyCode == KeyEvent.KEYCODE_DPAD_LEFT
					|| kyCode == KeyEvent.KEYCODE_DPAD_DOWN) {

				game_status = GameStatus.GAME_ON;
			} else if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
				init_game_data();
				drawReadyWindow();
				game_status = GameStatus.GAME_READY;
			}
		}
		if (game_status == GameStatus.GAME_READY) {
			// drawPausedWindow();
			/*
			 [Tags]* 显示快捷键菜单，并提示游戏已经ready
			 [Tags]*/
			if (keyCode == KeyEvent.KEYCODE_DPAD_RIGHT
					|| keyCode == KeyEvent.KEYCODE_DPAD_LEFT) {
				game_status = GameStatus.GAME_ON;
			} else if (keyCode == KeyEvent.KEYCODE_DPAD_DOWN) {
				game_status = GameStatus.GAME_OFF;
			}
			;
		}
		if (game_status == GameStatus.GAME_ON) {
			if (keyCode == KeyEvent.KEYCODE_DPAD_RIGHT) {
				if (bat_left + bat_width < ScreenW - 5) {

					bat_left += bat_move_speed;
					bat_right = ScreenW - bat_left;
				}
			} else if (keyCode == KeyEvent.KEYCODE_DPAD_LEFT) {
				if (bat_left > 5) {
					bat_left -= bat_move_speed;
					bat_right = ScreenW - bat_left;
				}
			} else if (keyCode == KeyEvent.KEYCODE_DPAD_DOWN) {
				game_status = GameStatus.GAME_PAUSED;
			}
		}
		return super.onKeyDown(keyCode, event);
	}
	private void drawRectFrame(Canvas canvas, Paint paint, int margin_left,
			int margin_right, int margin_top, int margin_bottom) {
		canvas.drawLine(margin_left, margin_top, margin_left, ScreenH
				- margin_bottom, paint);
		canvas.drawLine(margin_left, margin_top, ScreenW - margin_right,
				margin_top, paint);
		canvas.drawLine(ScreenW - margin_right, ScreenH - margin_bottom,
				ScreenW - margin_right, margin_top, paint);
		canvas.drawLine(margin_left, ScreenH - margin_bottom, ScreenW
				- margin_right, ScreenH - margin_bottom, paint);
	}
	private void drawGameMonitor(Canvas canvas, Paint paint) {
		paint.setColor(Color.WHITE);
		drawRectFrame(canvas, paint, 10, 10, ScreenH - 40, 10);
		paint.setTextSize(18);
		canvas.drawText("Time:" + (int) time, 20, ScreenH - 20, paint);
		canvas.drawText("Score:" + score, 110, ScreenH - 20, paint);
		canvas.drawText("Chances: " + chances, 200, ScreenH - 20, paint);
		paint.setTextSize(16);
	}
}
```