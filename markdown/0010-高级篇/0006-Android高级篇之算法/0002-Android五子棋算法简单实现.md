有一天在网上看到一个Android的五子棋，该程序的作者的GoogleTalk: lixinso@gmail.com。遂下载下来看看，可以下棋，但是没有实现电脑下棋算法，所以我一时兴起花了几个小时加了个电脑下棋算法在里面，很简单。原作者的游戏绘制就不多说了，主要讲电脑下棋算法，肯定还有问题的，也没有考虑先手禁手，欢迎拍砖:lol 。
#### 1、准备一个数组表示当前棋盘，另外准备两个数组分别保存电脑和玩家每个可下点的坐标及其分数(棋型数组)，每个可下点包括4个方向的分数，分别是横、竖、左斜、右斜。
以前的程序BUG太离谱了，竟然(0,0)的棋点玩家不能下棋，我这里修改了一下，并更新了源码。
```  
private int[][][] computerTable = new int[CHESS_GRID][CHESS_GRID][CHECK_DIR]; // 电脑棋形表
private int[][][] playerTable = new int[CHESS_GRID][CHESS_GRID][CHECK_DIR]; // 电脑棋形表
```
#### 2、每个可下点的4个方向分数判断，每个方向取当前点左右每边5个棋点的状态，然后分析它们是否构成五连、活四、活三等，每种棋型给予不同的分数。
```  
/**
 [Tags]* 分析存在五连
 [Tags]* @param tmpChess
 [Tags]*/
public boolean analyzeWulian(int[] tmpChess, int isWho) {
	int count = 0;
	for (int i = 0; i < HALF_LEN; i++) {
		if (tmpChess[HALF_LEN - (i + 1)] == isWho) {
			count++;
		} else {
			break;
		}
	}
	for (int i = 0; i < HALF_LEN; i++) {
		if (tmpChess[HALF_LEN + i] == isWho) {
			count++;
		} else {
			break;
		}
	}
	if (count == 4) {
		return true;
	}
	return false;
}

[Tags]/**
 [Tags]* 分析活四 return 是否存在活四
 [Tags]* @param tmpChess
 [Tags]*/
public boolean analyzeHuosi(int[] tmpChess, int isWho) {
	int count = 0;
	int i = 0;
	boolean isSpace = false;
	for (i = 0; i < HALF_LEN; i++) {
		if (tmpChess[HALF_LEN - (i + 1)] == isWho) {
			count++;
		} else {
			break;
		}
	}
	if (tmpChess[HALF_LEN - (i + 1)] == 0) {
		isSpace = true;
	}
	for (i = 0; i < HALF_LEN; i++) {
		if (tmpChess[HALF_LEN + i] == isWho) {
			count++;
		} else {
			break;
		}
	}
	if (tmpChess[HALF_LEN + i] == 0) {
		isSpace = true;
	} else {
		isSpace = false;
	}
	if (count == 3 && isSpace) {
		return true;
	}
	return false;
}
[Tags]/**
 [Tags]* 分析活三 return 是否存在活三
 [Tags]* @param tmpChess
 [Tags]*/
public boolean analyzeHuosan(int[] tmpChess, int isWho) {
	int count = 0;
	int i = 0;
	boolean isSpace = false;
	for (i = 0; i < HALF_LEN; i++) {
		if (tmpChess[HALF_LEN - (i + 1)] == isWho) {
			count++;
		} else {
			break;
		}
	}
	if (tmpChess[HALF_LEN - (i + 1)] == 0) {
		isSpace = true;
	}
	for (i = 0; i < HALF_LEN; i++) {
		if (tmpChess[HALF_LEN + i] == isWho) {
			count++;
		} else {
			break;
		}
	}
	if (tmpChess[HALF_LEN + i] == 0) {
		isSpace = true;
	} else {
		isSpace = false;
	}
	if (count == 2 && isSpace) {
		return true;
	}
	return false;
}
```
3、将玩家棋型数组和电脑棋型数组每个元素的分数比较，选出最大的五个放入一个降序排列的数组中。
```  
/**
 [Tags]* 找到最佳点
 [Tags]* @return 最佳点
 [Tags]*/
private ChessPoint findBestPoint() {
	int i, j;
	ChessPoint point;
	int maxScore = 0;
	int tmpScore = 0;
	for (i = 0; i < CHESS_GRID; i++) {
		for (j = 0; j < CHESS_GRID; j++) {
			// 电脑比较
			tmpScore = computerTable[i][j][0];
			tmpScore += computerTable[i][j][1];
			tmpScore += computerTable[i][j][2];
			tmpScore += computerTable[i][j][3];
			if (maxScore <= tmpScore) {
				maxScore = tmpScore;
				point = new ChessPoint();
				point.x = j;
				point.y = i;
				point.score = maxScore;
				if (maxScore > 0) {
					insertBetterChessPoint(point);
				}
			}
			// 玩家比较
			tmpScore = playerTable[i][j][0];
			tmpScore += playerTable[i][j][1];
			tmpScore += playerTable[i][j][2];
			tmpScore += playerTable[i][j][3];
			if (maxScore <= tmpScore) {
				maxScore = tmpScore;
				point = new ChessPoint();
				point.x = j;
				point.y = i;
				point.score = maxScore;
				if (maxScore > 0) {
					insertBetterChessPoint(point);
				}
			}
		}
	}
	// Log.v("cmaxpoint = ", "" + cMaxScore);
	// Log.v("pmaxpoint = ", "" + pMaxScore);
	return analyzeBetterChess();
}
```
#### 4、处理降序排列的数组，如果第一个元素的分数>=(必胜的条件的分数)，直接返回就可以了，如果小于就继续处理。
我们降序排列的数组每个元素，假设每个元素已下，然后判断其产生的后果，取出具有最佳后果的元素，并返回其值，作为电脑下棋点。判断每个元素的产生后果时，其实只需要处理其产生作用的棋盘范围就行了(以该元素位置为中心的正方形的棋盘范围，正方形边长为4 + 1 + 4，我用的10)，不必要处理搜索处理整个棋盘的棋子。
```  
private ChessPoint analyzeBetterChess() {
	if (fiveBetterPoints[0].score > 30) {
		return fiveBetterPoints[0];
	} else {
		ChessPoint betterPoint = null;
		ChessPoint tmpPoint = null;
		int goodIdx = 0;
		int i = 0;
		int startx, starty, endx, endy;
		ChessPoint[] fbpTmp = new ChessPoint[5];
		for (i = 0; i < 5; i++) {
			fbpTmp[i] = fiveBetterPoints[i];
		}
		for (i = 0; i < 5; i++) {
			if (fbpTmp[i] == null)
				break;
			mChessTable[fbpTmp[i].y][fbpTmp[i].x] = BLACK;
			clearChessArray();
			startx = fbpTmp[i].x - 5;
			starty = fbpTmp[i].y - 5;
			if (startx < 0) {
				startx = 0;
			}
			if (starty < 0) {
				starty = 0;
			}
			endx = startx + 10;
			endy = starty + 10;
			if (endx > CHESS_GRID) {
				endx = CHESS_GRID;
			}
			if (endy > CHESS_GRID) {
				endy = CHESS_GRID;
			}
			analyzeChessMater(computerTable, BLACK, startx, starty, endx,
					endy);
			// 分析玩家的棋型/////////////////////////////////////////////////////
			analyzeChessMater(playerTable, WHITE, startx, starty, endx,
					endy);
			tmpPoint = findBetterPoint(startx, starty, endx, endy);
			if (betterPoint != null) {
				if (betterPoint.score <= tmpPoint.score) {
					betterPoint = tmpPoint;
					goodIdx = i;
				}
			} else {
				betterPoint = tmpPoint;
				goodIdx = i;
			}
			mChessTable[fbpTmp[i].y][fbpTmp[i].x] = 0;
		}
		tmpPoint = null;
		betterPoint = null;
		return fbpTmp[goodIdx];
	}
}
```