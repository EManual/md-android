3. 实现状态转移
状态转移函数State_Change()非常简单，只需要根据状态转移图写几个条件判断，就能够实现。具体的代码如下：
```  
void State_Change() {
	switch (mMode) {
	case STATE_MENU: {
		if ((key_down & STATE_RESTART) > 0) {
			// 转换到关卡资源加载状态
			key_down -= STATE_RESTART;
			mMode = STATE_LOAD;
			pMode = 2;
		}
		break;
	}
	case STATE_PAUSE: {
		if ((key_down & STATE_RESUME) > 0) {
			// 转换到游戏执行状态
			key_down -= STATE_RESUME;
			mMode = STATE_RUNNING;
			// 修复由暂停造成的时间误差
			long k = System.currentTimeMillis() - pauseTime;
			startTime += k;
			endTime += k;
			lastTime_fast += k;
			lastTime_slow += k;
		}
		break;
	}
	case STATE_LOAD: {
		if (pMode == 0) {
			// 进入游戏资源加载状态
			pMode = 1;
		} else if (pMode == 1) {
			// 转换为游戏菜单状态
			mMode = STATE_MENU;
			pMode = 1;
		} else if (pMode == 2) {
			// 转换为游戏进行状态
			mMode = STATE_RUNNING;
			pMode = 0;
		}
		break;
	}
	case STATE_RUNNING: {
		if ((key_down & STATE_RESUME) > 0) {
			// 游戏暂停
			key_down &= 63 - STATE_RESUME;
			mMode = STATE_PAUSE;
		} else if (pMode > 0)
			pMode++;
		// 爆炸状态转换
		else {
			if (p_hurt > 0)
				pMode = 1;
			// 进入爆炸状态
		}
		if (pMode > 8) {
			// 爆炸特技完成，进入菜单状态
			mMode = STATE_MENU;
			pMode = 2;
		}
		break;
	}
	}
}
```
4. 实现Update_Fps()方法
Update_Fps()函数用来执行状态，实现主要的游戏逻辑。它的主框架代码如下：
```  
private void Update_Fps() {
	switch (mMode) {
	case STATE_LOAD: {
		if (pMode == 1) {
			load_game();
			// 加载游戏整体资源
		} else if (pMode == 2) {
			load_gate();
			// 加载关卡资源
		}
		break;
	}
	case STATE_MENU: {
		break;
	}
	case STATE_RUNNING: {
		pauseTime = -1;
		new_paodan();
		// 生成炮弹
		move();
		// "飞机"与炮弹的移动
		is_pengzhuang();
		// 碰撞判断
		break;
	}
	case STATE_PAUSE: {
		thead_pause();
		// 暂停
		pauseTime = System.currentTimeMillis();
		// 记录暂停时刻
		break;
	}
	}
}
```