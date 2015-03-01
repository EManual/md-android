由于代码比较多，所以Android 2D手机游戏即时阴影效果我们分为两片给大家分享一下，下面主要还是代码的部分，希望大家仔细阅读。不多说了，还是看代码吧：
查表法求三角函数：
```  
public final static int SIN_TABLE[] = { 0, 4, 8, 13, 17, 22, 26, 31, 35,
		39, 44, 48, 53, 57, 61, 65, 70, 74, 78, 83, 87, 91, 95, 99, 103,
		107, 111, 115, 119, 123, 127, 131, 135, 138, 142, 146, 149, 153,
		156, 160, 163, 167, 170, 173, 177, 180, 183, 186, 189, 192, 195,
		198, 200, 203, 206, 208, 211, 213, 216, 218, 220, 223, 225, 227,
		229, 231, 232, 234, 236, 238, 239, 241, 242, 243, 245, 246, 247,
		248, 249, 250, 251, 251, 252, 253, 253, 254, 254, 254, 254, 254,
		255 };
 /**
  * 查表法求SIN值
  * @param angle
  *            int 0到360度
  * @return int 返回SIN值的256倍
  */
public static int sin256(int angle) {
	angle %= 360;
	if (angle <= 90) {
		return SIN_TABLE[angle];
	} else if (angle <= 180) {
		return SIN_TABLE[180 - angle];
	} else if (angle <= 270) {
		return -SIN_TABLE[angle - 180];
	} else {
		return -SIN_TABLE[360 - angle];
	}
}
public static int cos256(int angle) {
	return sin256(angle + 90);
}
public static int cot256(int angle) {
	return cos256(angle) * 256 / Math.sin256(angle);
}
public static int tan256(int angle) {
	return sin256(angle) * 256 / Math.cos256(angle);
}
```
最后，有一个问题值得注意，细心观察自然界的影子，其实并不是直接旋转一下原图的效果，而是根据光源的位置投影而来的。网上有些那原图直接翻转的方法其实是错的，本文的方法也不太正确，但至少在2D游戏里看上去像那么回事，哈哈，看图为证~
![img](P)  