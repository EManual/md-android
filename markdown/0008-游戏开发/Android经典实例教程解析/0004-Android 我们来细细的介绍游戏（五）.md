move()函数需要实现"飞机"的移动和炮弹的移动，代码如下：
```  
public class Snippet {
	private void move() {
		/* *"飞机"移动 */
		if ((key_down & DIRECTION_UP) > 0)
			py -= STEP;
		if ((key_down & DIRECTION_DOWN) > 0)
			py += STEP;
		if ((key_down & DIRECTION_RIGHT) > 0)
			px += STEP;
		if ((key_down & DIRECTION_LEFT) > 0)
			px -= STEP;
		if (px > mCanvasWidth - mWidth)
			px = mCanvasWidth - mWidth;
		if (px < 0)
			px = 0;
		if (py > mCanvasHeight - mHeight)
			py = mCanvasHeight - mHeight;
		if (py < 0)
			py = 0;
		/* * 炮弹移动 */
		int i, k, x, y;
		double f;
		for (i = 0; i <= end1;) {
			x = data[k1 + i];
			// 炮弹x坐标
			i++;
			y = data[k1 + i];
			// 炮弹y坐标
			i++;
			f = (double) (data[k1 + i]);
			ff = f * Math.PI / 180;
			// 炮弹运动方向
			if ((data[k1 + i] & (1 << 10)) > 0)
				k = (int) (4 + (endTime - startTime) / 2000);
			else
				k = (int) (2 + (endTime - startTime) / 3000);
			k *= 10;
			// 运动距离
			i++;
			x += k * Math.sin(f);
			y -= k * Math.cos(f);
			if (x > -80 && x < mCanvasWidth * 10 && y > -80
					&& y < mCanvasHeight * 10) {
				// 炮弹没有出界
				end2++;
				data[k2 + end2] = x;
				end2++;
				data[k2 + end2] = y;
				end2++;
				data[k2 + end2] = data[k1 + i - 1];
			}
		}
		/* * 下面的代码用于维护滚动数组，读者可以先跳过 */
		end1 = end2;
		end2 = -1;
		k1 ^= (1 << 12) + (1 << 13);
		k2 ^= (1 << 12) + (1 << 13);
	}
}
```
代码解释：
炮弹的坐标不是炮弹中心的坐标，而是炮弹左上角的坐标，炮弹的大小为8×8像素。因此，炮弹所处的矩形空间为[x,x+8]，[y,y+8]。此外，为了提高精度，坐标值被扩大了10倍，详细分析见9.4.2节的内容。因此，判断炮弹出界的条件为：x>-80，且x<mCanvasWidth*10，且y>-80，且y<mCanvasHeight*10。
碰撞判断往往是游戏开发中最复杂，同时也是最有意思的部分。在《是男人就坚持20秒》游戏中，实现"飞机"与炮弹的碰撞可以采用一种非常简单，但是也很粗糙的矩形碰撞方法。矩形碰撞可以描述为：若两个矩形的顶点坐标分别为(x1（横坐标），y1（纵坐标），x2（横坐标，且x2>x1），y2（纵坐标，且y2>y1）)，x3（横坐标），y3（纵坐标），x4（横坐标，且x4>x3），y4（纵坐标，且y4>y3）)，如果同时满足下列4个条件，则发生碰撞。
x1<x4；
x2>x3；
y1<y4；
y2>y3。
矩形碰撞判断的伪代码如下：
```  
if (x1<x4&&x2>x3&&y1<y4&&y2>y3) { //发生碰撞 碰撞处理 }
```
thead_pause()函数很简单，只需要修改游戏状态即可。
```  
private void thead_pause() { mMode=STATE_PAUSE; }
```
到此为止，Update_Fps()方法已经完成了，接下来实现最后一个方法doDraw()。