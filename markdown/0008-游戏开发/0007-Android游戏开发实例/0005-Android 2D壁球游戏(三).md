代码如下：
```  
private void drawOffWindow() {
	if (off_count == 7) {
		main_activity.finish();
	}
	try {
		canvas = sfh.lockCanvas();
		paint.setColor(Color.WHITE);
		canvas.drawColor(Color.DKGRAY);
		drawRectFrame(canvas, paint, 50, 50, 100, 250);
		Paint title_paint = new Paint();
		title_paint.setTextSize(20);
		title_paint.setAntiAlias(true);
		title_paint.setColor(Color.RED);
		canvas.drawText("Game is Exiting", 93, 140, title_paint);
		title_paint.setColor(Color.GREEN);
		for (int i = 0; i < off_count; i++) {
			canvas.drawCircle(80 + i * 28, 160, 5, title_paint);
		}
		off_count++;
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (canvas != null) {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
}
private void drawOverWindow() {
	try {
		canvas = sfh.lockCanvas();
		paint.setColor(Color.WHITE);
		canvas.drawColor(Color.DKGRAY);
		drawRectFrame(canvas, paint, 50, 50, 100, 200);
		Paint title_paint = new Paint();
		title_paint.setTextSize(20);
		title_paint.setAntiAlias(true);
		title_paint.setColor(Color.YELLOW);
		canvas.drawText("Your Total Score", 90, 140, title_paint);
		// canvas.drawText("[YOUR SCORE]: 0000", 100, 150, paint);
		// paint.setColor(Color.RED);
		// canvas.drawText("[score] ", 130, 170, paint);
		title_paint.setTextSize(43);
		title_paint.setColor(Color.RED);
		canvas.drawText("" + score, 120, 195, title_paint);
		paint.setColor(Color.WHITE);
		canvas.drawText("Press [UP] to continue", 80, 230, paint);
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (canvas != null) {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
}
private void drawChancesWindow() {
	try {
		canvas = sfh.lockCanvas();
		paint.setColor(Color.WHITE);
		canvas.drawColor(Color.DKGRAY);
		drawRectFrame(canvas, paint, 15, 15, 120, 200);
		Paint title_paint = new Paint();
		title_paint.setTextSize(20);
		title_paint.setAntiAlias(true);
		title_paint.setColor(Color.YELLOW);
		canvas.drawText("You have chances left!", 35, 210, title_paint);
		title_paint.setTextSize(50);
		title_paint.setColor(Color.RED);
		canvas.drawText("" + chances, 130, 220, title_paint);
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (canvas != null) {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
}
private void init_game_data() {
	head_left = (int) (Math.random() * ScreenW - 2 * radius - 20);
	head_top = 41;
	if (head_left <= 20) {
		head_left = 21;
	}
	randomBallData();
	bat_left = ScreenW / 2;
	score = 0;
	chances = 3;
}
@Override
public boolean onKeyUp(int keyCode, KeyEvent event) {
	return super.onKeyUp(keyCode, event);
}
public void draw() throws InterruptedException {
	if (game_status == GameStatus.GAME_ON) {
		drawMainWindow();
	} else if (game_status == GameStatus.GAME_OVER) {
		drawOverWindow();
	} else if (game_status == GameStatus.GAME_PAUSED) {
		drawPausedWindow();
	} else if (game_status == GameStatus.GAME_OFF) {
		drawOffWindow();
		try {
			Thread.sleep(500);
			// main_activity.finish();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	} else if (game_status == GameStatus.GAME_CHANCES) {
		drawChancesWindow();
		Thread.sleep(2000);
		game_status = GameStatus.GAME_ON;
	}
}
@Override
public void run() {
	while (true) {
		time += 0.1;
		try {
			draw();
			Thread.sleep(refresh_circle_time);
			// Thread.sleep(100000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
private void randomBallData() {
	int x = moveSpeedX / Math.abs(moveSpeedX);
	int y = moveSpeedY / Math.abs(moveSpeedY);
	ball_score = (int) (Math.random() * 100);
	if (ball_score == 0) {
		ball_score = 100;
	}
	if (ball_score < 30) {
		ball_color = Color.LTGRAY;
		moveSpeedX = 12;
		moveSpeedY = 12;
	} else if (ball_score >= 30 && ball_score < 50) {
		ball_color = Color.WHITE;
		moveSpeedX = 14;
		moveSpeedY = 14;
	} else if (ball_score >= 50 && ball_score < 70) {
		ball_color = Color.YELLOW;
		moveSpeedX = 16;
		moveSpeedY = 16;
	} else if (ball_score >= 70 && ball_score < 90) {
		ball_color = Color.BLUE;
		moveSpeedX = 18;
		moveSpeedY = 18;
	} else if (ball_score >= 90 && ball_score <= 100) {
		ball_color = Color.RED;
		moveSpeedX = 20;
		moveSpeedY = 20;
	}
	moveSpeedX *= x;
	moveSpeedY *= y;
}
@Override
public void surfaceChanged(SurfaceHolder holder, int format, int width,
		int height) {
}
@Override
public void surfaceCreated(SurfaceHolder holder) {
	init_game_data();
	ScreenW = this.getWidth();
	ScreenH = this.getHeight();
	head_left = 21;
	head_top = 41;
	bat_left = 21;
	bat_right = ScreenW - bat_left;
	drawReadyWindow();
	th.start();
}
@Override
public void surfaceDestroyed(SurfaceHolder holder) {
}
```