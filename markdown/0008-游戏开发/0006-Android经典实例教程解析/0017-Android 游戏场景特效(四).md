这样4个动画效果已经介绍完毕， 下面我将一些重要的代码贴上。
根据特效的状态 进行绘制特效 
```  
[Tags]/** 绘制特效 **/
public void RenderEffect() {
	switch (s_effectType) {
	case RANDOM_EFFECT_TYPE_SQUARE:
		[Tags]/** 百叶窗效果利用双for循环 修改每个矩形绘制的宽度 **/
		for (int i = 0; i <= (mScreenWidth / RANDOM_TYPE_0_RANGE); i++) {
			for (int j = 0; j <= (mScreenHeight / RANDOM_TYPE_0_RANGE); j++) {
				drawFillRect(mCanvas, Color.BLACK, i * RANDOM_TYPE_0_RANGE,
						j * RANDOM_TYPE_0_RANGE, s_effRange, s_effRange);
			}
		}
		break;
	case RANDOM_EFFECT_TYPE_SHADOW:
		[Tags]/** 水纹效果其实绘制了4个矩形 中间留一些缝隙 **/
		drawFillRect(mCanvas, Color.BLACK, 0, 0, s_effRange, mScreenHeight);
		drawFillRect(mCanvas, Color.BLACK, s_effRange
				+ RANDOM_TYPE_1_SPACE1, 0, RANDOM_TYPE_1_RANGE1,
				mScreenHeight);
		drawFillRect(mCanvas, Color.BLACK, s_effRange
				+ RANDOM_TYPE_1_SPACE2, 0, RANDOM_TYPE_1_RANGE2,
				mScreenHeight);
		drawFillRect(mCanvas, Color.BLACK, s_effRange
				+ RANDOM_TYPE_1_SPACE3, 0, RANDOM_TYPE_1_RANGE3,
				mScreenHeight);
		break;
	case RANDOM_EFFECT_TYPE_CROSS:
		[Tags]/** 交错的实现矩形相交 **/
		int count = (mScreenHeight / RANDOM_TYPE_2_RANGE);
		for (int i = 0; i < count; i += 2) {
			drawFillRect(mCanvas, Color.BLACK, 0, i * RANDOM_TYPE_2_RANGE,
					s_effRange, RANDOM_TYPE_2_RANGE);
		}
		for (int i = 1; i < count; i += 2) {
			drawFillRect(mCanvas, Color.BLACK, mScreenWidth - s_effRange, i
					[Tags]* RANDOM_TYPE_2_RANGE, s_effRange, RANDOM_TYPE_2_RANGE);
		}
		break;
	case RANDOM_EFFECT_TYPE_SECTOR:
		// rectf为扇形绘制区域 为了让扇形完全填充屏幕所以将它的区域扩大了100像素
		RectF rectf = new RectF(-RANDOM_TYPE_3_RANGE, -RANDOM_TYPE_3_RANGE,
				mScreenWidth + RANDOM_TYPE_3_RANGE, mScreenHeight
						+ RANDOM_TYPE_3_RANGE);

		// 将扇形绘制出来
		drawFillCircle(mCanvas, Color.BLACK, rectf, 0, s_effRange, true);
		break;
	}
}
```
在播放动画的时候须要更新游戏特效 主要是用来更新特效绘制的参数 根据时时更新的参数在绘制中让特效动画动起来。
```  
[Tags]/** 更新特效 **/
public void UpdataEffectRange(int range) {
	if (s_effRange < s_effectRangeTarget) {
		s_effRange += range;
		if (s_effRange > s_effectRangeTarget) {
			s_effRange = s_effectRangeTarget;
		}
	} else if (s_effRange > s_effectRangeTarget) {
		s_effRange -= range;
		if (s_effRange < s_effectRangeTarget) {
			s_effRange = s_effectRangeTarget;
		}
	}
}
```
通过点击图片按钮来设置播放特效的类型 在这里初始化当前需要播放的 特效绘制的相关参数。
```  
[Tags]/** 设置播放特效类型 **/
public void SetCurtainEffect(int type) {
	s_effectType = type;
	switch (s_effectType) {
	case RANDOM_EFFECT_TYPE_SQUARE:
		s_effRange = 0;
		s_effectRangeTarget = RANDOM_TYPE_0_RANGE;
		break;
	case RANDOM_EFFECT_TYPE_SHADOW:
		s_effRange = EFFECT_RANGE_PERFRAME_1;
		s_effectRangeTarget = mScreenWidth;
		break;
	case RANDOM_EFFECT_TYPE_CROSS:
		s_effRange = 0;
		s_effectRangeTarget = mScreenWidth;
		break;
	case RANDOM_EFFECT_TYPE_SECTOR:
		s_effRange = 0;
		s_effectRangeTarget = 360;
		break;
	}
	setGameState(GAME_EFFECT);
}
```