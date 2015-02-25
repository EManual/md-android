Update_Fps()方法中有6个子函数需要实现：load_game()、load_gate()、new_paodan()、move()、is_pengzhuang()和thead_pause()。
接下来，将逐一实现这几个函数。
load_game()函数需要完成4个方面的初始化工作：加载图片、初始化画笔、碰撞预处理和预生成随机数。其中，碰撞预处理比较复杂，具体分析与代码实现将在9.4.1节中给出。下面给出其他三部分的代码。
```  
private void load_game() {
	/* * 加载图片资源 */
	Resources res = mContext.getResources();
	p = res.getDrawable(R.drawable.icon);
	bomb1 = res.getDrawable(com.example.R.drawable.bomb01);
	bomb2 = res.getDrawable(com.example.R.drawable.bomb02);
	bomb3 = res.getDrawable(com.example.R.drawable.bomb03);
	bomb4 = res.getDrawable(com.example.R.drawable.bomb04);
	bomb5 = res.getDrawable(com.example.R.drawable.bomb05);
	bomb6 = res.getDrawable(com.example.R.drawable.bomb06);
	bomb7 = res.getDrawable(com.example.R.drawable.bomb07);
	bomb8 = res.getDrawable(com.example.R.drawable.bomb08);
	paodan = res.getDrawable(com.example.R.drawable.dd);
	mBackgroundImage = BitmapFactory
			.decodeResource(res, R.drawable.bg_game);
	pBitmap = BitmapFactory.decodeResource(res, R.drawable.icon);
	mWidth = p.getIntrinsicWidth();
	mHeight = p.getIntrinsicHeight();
	/* * 初始化画笔 */
	paint = new Paint();
	paint.setAntiAlias(true);
	paint.setARGB(255, 0, 255, 0);
	paint.setTextSize(28);
	paint2 = new Paint();
	paint2.setAntiAlias(true);
	paint2.setARGB(200, 0, 255, 0);
	paint2.setTextSize(18);
	/* * 一种简单易行，并且离散性较优的初级随机数产生方法 */
	randData = new int[1000];
	randEnd = -1;
	k = (int) (Math.random() * 1000);
	while (randEnd < 1000 - 1) {
		randEnd++;
		k *= 691;
		k += 863;
		k %= 997;
		randData[randEnd] = k;
	}
	randCurr = 0;
}
```
代码解释：
最后一段代码用来为《是男人就坚持20秒》量身打造一组"随机"数。
生成的"随机"数保存在数组randData中，"随机数"的取值范围在0～1000之间。
随机数生成规则：若前一个随机数为k，则下一个随机数为：(k*691+863)%997。