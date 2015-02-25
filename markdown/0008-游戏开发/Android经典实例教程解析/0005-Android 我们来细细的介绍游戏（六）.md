实现doDraw()方法
在不同的游戏状态需要绘制不同的内容。游戏运行状态的图像绘制是doDraw（）方法中最重要，也是最复杂的一部分。对于《是男人就坚持20秒》游戏，游戏运行状态一共需要绘制以下4个内容：
绘制背景；
绘制炮弹；
绘制"飞机"，或者是爆炸特效；
绘制坚持时间。
doDraw()方法的实现代码如下：
```  
private void doDraw(Canvas c) {
	int i;
	switch (mMode) {
	case STATE_RUNNING:// 游戏运行状态 {
		// 绘制背景
		c.drawBitmap(mBackgroundImage, 0, 0, null);
		// 绘制炮弹
		for (i = 0; i <= end1; i += 3) {
			paodan.setBounds(data[k1 + i] / 10, data[k1 + i + 1] / 10,
					data[k1 + i] / 10 + 8, data[k1 + i + 1] / 10 + 8);
			paodan.draw(c);
		} // 绘制“飞机”
		if (pMode < 1) {
			p.setBounds(px, py, px + mWidth, py + mHeight);
			p.draw(c);
		}
		// 爆炸特效
		// 下面的这种实现方式比较笨拙，当特效图片较多时，可以把图片放到一个数组中，
		// 以缩短代码量
		else if (pMode == 1) {
			bomb1.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb1.draw(c);
		} else if (pMode == 2) {
			bomb2.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb2.draw(c);
		} else if (pMode == 3) {
			bomb3.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb3.draw(c);
		} else if (pMode == 4) {
			bomb4.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb4.draw(c);
		} else if (pMode == 5) {
			bomb5.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb5.draw(c);
		} else if (pMode == 6) {
			bomb6.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb6.draw(c);
		} else if (pMode == 7) {
			bomb7.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb7.draw(c);
		} else if (pMode == 1) {
			bomb8.setBounds(px - 20, py - 10, px + 60, py + 70);
			bomb8.draw(c);
		}
		// 在屏幕左上角绘制游戏时间
		c.drawText("坚挺时间：" + ((endTime - startTime) / 1000) + ".
				+ (((endTime - startTime) % 1000) / 100), 5, 25, paint2);
		break;
	case STATE_MENU: {
		c.drawBitmap(mBackgroundImage, 0, 0, null);
		if (pMode == 1) {
			// 刚进入游戏时的界面
			// 绘制"欢迎进入游戏" 代码略。
		} else {
			// 完成游戏之后的界面
			// 显示生存时间，并评分 代码略。
		}
		break;
	}
	case STATE_PAUSE: {
		// 显示游戏暂停
		c.drawText("游戏暂停", mCanvasWidth / 3, mCanvasHeight / 3 + 30, paint);
		break;
	}
	}
}
```