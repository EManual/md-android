代码如下：
```  
private void drawMainWindow() {
	try {
		canvas = sfh.lockCanvas();
		Paint ball_paint = new Paint();
		ball_paint.setAntiAlias(true);
		ball_paint.setColor(ball_color);
		canvas.drawColor(Color.DKGRAY);
		canvas.drawLine(0, 0, ScreenW / 5 - 20, ScreenH / 5 - 20,
				ball_paint);
		canvas.drawLine(ScreenW, 0, 4 * ScreenW / 5 + 20, ScreenH / 5 - 20,
				ball_paint);
		canvas.drawLine(ScreenW / 5 - 20, ScreenH / 5 - 20,
				4 * ScreenW / 5 + 20, ScreenH / 5 - 20, ball_paint);
		canvas.drawLine(ScreenW / 5 - 20, ScreenH / 5 - 20, 0, ScreenH,
				ball_paint);
		canvas.drawLine(ScreenW * 4 / 5 + 20, ScreenH / 5 - 20, ScreenW,
				ScreenH, ball_paint);
		canvas.drawCircle(head_left, head_top, radius, ball_paint);
		canvas.drawRect(bat_left, ScreenH - bat_ply - 100, bat_left
				+ bat_width, ScreenH - 90, paint);
		paint.setColor(Color.RED);
		canvas.drawLine(0, ScreenH - bat_ply - 100, ScreenW, ScreenH
				- bat_ply - 100, paint);
		drawGameMonitor(canvas, paint);
		paint.setColor(Color.RED);
		// canvas.drawLine(0, ScreenH-92, ScreenW, ScreenH-92, paint);
		head_left += moveSpeedX;
		head_top += moveSpeedY;
		paint.setColor(Color.WHITE);
		if (show_score) {
			ball_paint.setTextSize(50);
			canvas.drawText("+" + ball_score, ScreenW / 2 - 50,
					ScreenH / 2, ball_paint);
		}
		// paint.setColor(Color.WHITE);
		if (head_left >= ScreenW - 2 * radius - 20) {
			// Right edge
			moveSpeedX *= -1;
			show_score = false;
			head_left = ScreenW - 2 * radius - 20;
		}
		if (head_left <= 20) {
			// Left edge
			moveSpeedX *= -1;
			show_score = false;
			head_left = 20;
		}
		if (head_top <= 40) {
			// Top edge
			moveSpeedY *= -1;
			randomBallData();
			show_score = false;
			head_top = 40;
		}
		if (head_top >= ScreenH - 2 * radius - 100) {
			float ball_current_pos = (float) (head_left + radius);
			if (ball_current_pos >= bat_left
					&& ball_current_pos <= bat_left + bat_width) {
				tempSpeedX = moveSpeedX;
				tempSpeedY = moveSpeedY;
				moveSpeedY *= -1;
				score += ball_score;
				show_score = true;
				score_count = 100;
			} else {
				chances--;
				if (chances > 0) {
					game_status = GameStatus.GAME_CHANCES;
					head_left = (int) (Math.random() * ScreenW - 2 * radius - 20);
					head_top = 41;
					if (head_left <= 20) {
						head_left = 21;
					}
					randomBallData();
				} else
					game_status = GameStatus.GAME_OVER;
			}
		}
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (canvas != null) {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
}
private void drawPausedWindow() {
	try {
		canvas = sfh.lockCanvas();
		paint.setColor(Color.WHITE);
		canvas.drawColor(Color.DKGRAY);
		drawRectFrame(canvas, paint, 50, 50, 100, 220);
		Paint title_paint = new Paint();
		title_paint.setTextSize(20);
		title_paint.setAntiAlias(true);
		title_paint.setColor(Color.YELLOW);
		canvas.drawText("Game is paused!", 78, 160, title_paint);
		canvas.drawText("Press any key to continue", 70, 200, paint);
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (canvas != null) {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
}
private void drawReadyWindow() {
	try {
		canvas = sfh.lockCanvas();
		paint.setColor(Color.WHITE);
		canvas.drawColor(Color.DKGRAY);
		Paint title_paint = new Paint();
		// canvas.drawRect(50, 100, 200, 200, paint);
		drawRectFrame(canvas, paint, 10, 10, 10, 10);
		// drawRectFrame(canvas,paint,20,20,20,20);
		canvas.drawText("WELCOME TO PAY MY GAME", 50, 100, paint);
		canvas.drawText("------Copyright by Skyrain-------", 42, 300, paint);
		title_paint.setTextSize(35);
		title_paint.setAntiAlias(true);
		title_paint.setColor(Color.WHITE);
		title_paint.setTypeface(Typeface.SANS_SERIF);
		canvas.drawText("Tennis Game", 55, 250, title_paint);
		Resources res = getResources();
		Bitmap bmp = BitmapFactory.decodeResource(res, R.drawable.icon);
		canvas.drawBitmap(bmp, 120, 150, title_paint);
		paint.setTextSize(10);
		canvas.drawText("************************", 80, 350, paint);
		canvas.drawText("* [Left]or[Right]: Start/Move", 80, 365, paint);
		canvas.drawText("*", 220, 365, paint);
		canvas.drawText("* [Down]: Exit", 80, 380, paint);
		canvas.drawText("*", 220, 380, paint);
		canvas.drawText("************************", 80, 395, paint);
		// Thread.sleep(3000);
		// game_status=GameStatus.GAME_READY;
		paint.setTextSize(16);
	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		if (canvas != null) {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
}
```