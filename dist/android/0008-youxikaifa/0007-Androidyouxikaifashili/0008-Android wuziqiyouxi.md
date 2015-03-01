在这里我给大家分享一下android的一个小游戏，这个小游戏就是五子棋。写完以后感觉Android的SDK，虽然也是使用Java的，但是跟Java ME还是有很大不一样。首先就是Android的SDK没有实现所有的Java ME标准，原来运行在KJava上的应用程序是不能在Android上直接跑的。另外就是Android的SDK有大量的API是Android自己的，需要开发人员去了解。
Android的开发框架也跟别的不一样，需要学习一下。这个五子棋游戏是我参照Android的Snake这个Demo还有别的例子，加上自己的需求写出来的。其中实现了棋盘、下棋、判断输赢、重新开局等功能。目前暂时没有实现机器智能走棋子的功能。Android的触屏功能是比较好用的，触屏很好用，而且Android的“Window”窗、”Shade”帘加上触摸，显得很炫。这个五子棋，也是用触摸屏实现走棋的。点一下棋盘的位子，把棋子落到棋盘上。
我们就先来看看效果图吧。
![img](P)  
我们下面来看看代码
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.TextView;
//这是主程序，继承自Activity，实现onCreate方法。：
public class gobang extends Activity {
	GobangView gbv;
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		gbv = (GobangView) this.findViewById(R.id.gobangview);
		gbv.setTextView((TextView) this.findViewById(R.id.text));
	}
}
```
XML如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <lixinsong.game.gobang.GobangView
        android:id="@+id/gobangview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        tileSize="24"
        android:text="aaaaa" />
    <RelativeLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true" >
        <TextView
            android:id="@+id/text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:gravity="center_horizontal"
            android:text="hahahhaha"
            android:textColor="ffff0000"
            android:textSize="24sp"
            android:textStyle="bold"
            android:visibility="visible" />
    </RelativeLayout>
</FrameLayout>
```
下面我们就来看看这个游戏的主要代码：
```  
import android.content.Context;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Paint.Style;
import android.graphics.drawable.Drawable;
import android.util.AttributeSet;
import android.util.Log;
import android.view.KeyEvent;
import android.view.MotionEvent;
import android.view.View;
import android.widget.TextView;
/*棋盘一共10×10格 
  * 棋盘居中
  */
public class GobangView extends View {
	protected static int GRID_SIZE = 10;
	protected static int GRID_WIDTH = 30; // 棋盘格的宽度
	protected static int CHESS_DIAMETER = 26; // 棋的直径
	protected static int mStartX;// 棋盘定位的左上角X
	protected static int mStartY;// 棋盘定位的左上角Y
	private Bitmap[] mChessBW; // 黑棋和白棋
	private static int[][] mGridArray; // 网格
	boolean key = false;
	int wbflag = 1; // 该下白棋了=2，该下黑棋了=1. 这里先下黑棋（黑棋以后设置为机器自动下的棋子）
	int mLevel = 1; // 游戏难度
	int mWinFlag = 0;
	private final int BLACK = 1;
	private final int WHITE = 2;
	int mGameState = GAMESTATE_RUN; // 游戏阶段：0=尚未游戏，1=正在进行游戏，2=游戏结束
	static final int GAMESTATE_PRE = 0;
	static final int GAMESTATE_RUN = 1;
	static final int GAMESTATE_PAUSE = 2;
	static final int GAMESTATE_END = 3;
	// private TextView mStatusTextView; // 根据游戏状态设置显示的文字
	public TextView mStatusTextView; // 根据游戏状态设置显示的文字
	private Bitmap btm1;
	private final Paint mPaint = new Paint();
	CharSequence mText;
	CharSequence STRING_WIN = "White win! \n Press Fire Key to start new game.";
	CharSequence STRING_LOSE = "Black win! \n Press Fire Key to start new game.";
	CharSequence STRING_EQUAL = "Cool! You are equal! \n Press Fire Key to start new Game.";
	public GobangView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	public GobangView(Context context, AttributeSet attrs) { // 调用的是这个构造函数
		super(context, attrs);
		this.setFocusable(true); // 20090530
		this.setFocusableInTouchMode(true);
		init();
	}
	// 这里画棋子后来没有用图片画，而是直接画了圆。因为我做的图片不好看。
	// 初始化黑白棋的Bitmap
	public void init() {
		mGameState = 1; // 设置游戏为开始状态
		wbflag = BLACK; // 初始为先下黑棋
		mWinFlag = 0; // 清空输赢标志。
		mGridArray = new int[GRID_SIZE - 1][GRID_SIZE - 1];
		mChessBW = new Bitmap[2];
		Bitmap bitmap = Bitmap.createBitmap(CHESS_DIAMETER, CHESS_DIAMETER,
				Bitmap.Config.ARGB_8888);
		Canvas canvas = new Canvas(bitmap);
		Resources r = this.getContext().getResources();
		Drawable tile = r.getDrawable(R.drawable.chess1);
		tile.setBounds(0, 0, CHESS_DIAMETER, CHESS_DIAMETER);
		tile.draw(canvas);
		mChessBW[0] = bitmap;
		tile = r.getDrawable(R.drawable.chess2);
		tile.setBounds(0, 0, CHESS_DIAMETER, CHESS_DIAMETER);
		tile.draw(canvas);
		mChessBW[1] = bitmap;
	}
	public void setTextView(TextView tv) {
		mStatusTextView = tv;
		mStatusTextView.setVisibility(View.INVISIBLE);
	}
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		mStartX = w / 2 - GRID_SIZE * GRID_WIDTH / 2;
		mStartY = h / 2 - GRID_SIZE * GRID_WIDTH / 2;
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		switch (mGameState) {
		case GAMESTATE_PRE:
			break;
		case GAMESTATE_RUN: {
			int x;
			int y;
			float x0 = GRID_WIDTH - (event.getX() - mStartX) % GRID_WIDTH;
			float y0 = GRID_WIDTH - (event.getY() - mStartY) % GRID_WIDTH;
			if (x0 < GRID_WIDTH / 2) {
				x = (int) ((event.getX() - mStartX) / GRID_WIDTH);
			} else {
				x = (int) ((event.getX() - mStartX) / GRID_WIDTH) - 1;
			}
			if (y0 < GRID_WIDTH / 2) {
				y = (int) ((event.getY() - mStartY) / GRID_WIDTH);
			} else {
				y = (int) ((event.getY() - mStartY) / GRID_WIDTH) - 1;
			}
			if ((x >= 0 && x < GRID_SIZE - 1) && (y >= 0 && y < GRID_SIZE - 1)) {
				if (mGridArray[x][y] == 0) {
					if (wbflag == BLACK) {
						putChess(x, y, BLACK);
						// this.mGridArray[x][y] = 1;
						if (checkWin(BLACK)) { // 如果是黑棋赢了
							mText = STRING_LOSE;
							mGameState = GAMESTATE_END;
							showTextView(mText);
						} else if (checkFull()) {// 如果棋盘满了
							mText = STRING_EQUAL;
							mGameState = GAMESTATE_END;
							showTextView(mText);
						}
						wbflag = WHITE;
					} else if (wbflag == WHITE) {
						putChess(x, y, WHITE);
						// this.mGridArray[x][y] = 2;
						if (checkWin(WHITE)) {
							mText = STRING_WIN;
							mGameState = GAMESTATE_END;
							showTextView(mText);
						} else if (checkFull()) {// 如果棋盘满了
							mText = STRING_EQUAL;
							mGameState = GAMESTATE_END;
							showTextView(mText);
						}
						wbflag = BLACK;
					}
				}
			}
		}
			break;
		case GAMESTATE_PAUSE:
			break;
		case GAMESTATE_END:
			break;
		}
		this.invalidate();
		return true;
	}

	@Override
	public boolean onKeyDown(int keyCode, KeyEvent msg) {
		Log.e("KeyEvent.KEYCODE_DPAD_CENTER", " " + keyCode);
		if (keyCode == KeyEvent.KEYCODE_DPAD_CENTER) {
			switch (mGameState) {
			case GAMESTATE_PRE:
				break;
			case GAMESTATE_RUN:
				break;
			case GAMESTATE_PAUSE:
				break;
			case GAMESTATE_END: {// 游戏结束后，按CENTER键继续
				Log.e("Fire Key Pressed:::", "FIRE");
				mGameState = GAMESTATE_RUN;
				this.setVisibility(View.VISIBLE);
				this.mStatusTextView.setVisibility(View.INVISIBLE);
				this.init();
				this.invalidate();
			}
				break;
			}
		}
		return super.onKeyDown(keyCode, msg);
	}
	@Override
	public void onDraw(Canvas canvas) {
		canvas.drawColor(Color.YELLOW);
		// 画棋盘
		{
			Paint paintRect = new Paint();
			paintRect.setColor(Color.GRAY);
			paintRect.setStrokeWidth(2);
			paintRect.setStyle(Style.STROKE);
			for (int i = 0; i < GRID_SIZE; i++) {
				for (int j = 0; j < GRID_SIZE; j++) {
					int mLeft = i * GRID_WIDTH + mStartX;
					int mTop = j * GRID_WIDTH + mStartY;
					int mRright = mLeft + GRID_WIDTH;
					int mBottom = mTop + GRID_WIDTH;
					canvas.drawRect(mLeft, mTop, mRright, mBottom, paintRect);
				}
			}
			// 画棋盘的外边框
			paintRect.setStrokeWidth(4);
			canvas.drawRect(mStartX, mStartY, mStartX + GRID_WIDTH * GRID_SIZE,
					mStartY + GRID_WIDTH * GRID_SIZE, paintRect);
		}
		// 画棋子
		for (int i = 0; i < GRID_SIZE - 1; i++) {
			for (int j = 0; j < GRID_SIZE - 1; j++) {
				if (mGridArray[i][j] == BLACK) {
					// 通过图片来画
					// canvas.drawBitmap(mChessBW[0], mStartX + (i+1) *
					// GRID_WIDTH - CHESS_DIAMETER/2 , mStartY + (j+1)*
					// GRID_WIDTH - CHESS_DIAMETER/2 , mPaint);
					// 通过圆形来画
					{
						Paint paintCircle = new Paint();
						paintCircle.setColor(Color.BLACK);
						canvas.drawCircle(mStartX + (i + 1) * GRID_WIDTH,
								mStartY + (j + 1) * GRID_WIDTH,
								CHESS_DIAMETER / 2, paintCircle);
					}
				} else if (mGridArray[i][j] == WHITE) {
					// 通过图片来画
					// canvas.drawBitmap(mChessBW[1], mStartX + (i+1) *
					// GRID_WIDTH - CHESS_DIAMETER/2 , mStartY + (j+1)*
					// GRID_WIDTH - CHESS_DIAMETER/2 , mPaint);

					// 通过圆形来画
					{
						Paint paintCircle = new Paint();
						paintCircle.setColor(Color.WHITE);
						canvas.drawCircle(mStartX + (i + 1) * GRID_WIDTH,
								mStartY + (j + 1) * GRID_WIDTH,
								CHESS_DIAMETER / 2, paintCircle);
					}
				}
			}
		}
	}
	public void putChess(int x, int y, int blackwhite) {
		mGridArray[x][y] = blackwhite;
	}
	public boolean checkWin(int wbflag) {
		for (int i = 0; i < GRID_SIZE - 1; i++)
			// i表示列(根据宽度算出来的)
			for (int j = 0; j < GRID_SIZE - 1; j++) {// i表示行(根据高度算出来的)
			// 检测横轴五个相连
				if (((i + 4) < (GRID_SIZE - 1)) && (mGridArray[i][j] == wbflag)
						&& (mGridArray[i + 1][j] == wbflag)
						&& (mGridArray[i + 2][j] == wbflag)
						&& (mGridArray[i + 3][j] == wbflag)
						&& (mGridArray[i + 4][j] == wbflag)) {
					Log.e("check win or loss:", wbflag + "win");
					mWinFlag = wbflag;
				}
				// 纵轴5个相连
				if (((j + 4) < (GRID_SIZE - 1)) && (mGridArray[i][j] == wbflag)
						&& (mGridArray[i][j + 1] == wbflag)
						&& (mGridArray[i][j + 2] == wbflag)
						&& (mGridArray[i][j + 3] == wbflag)
						&& (mGridArray[i][j + 4] == wbflag)) {
					Log.e("check win or loss:", wbflag + "win");
					mWinFlag = wbflag;
				}
				// 左上到右下5个相连
				if (((j + 4) < (GRID_SIZE - 1)) && ((i + 4) < (GRID_SIZE - 1))
						&& (mGridArray[i][j] == wbflag)
						&& (mGridArray[i + 1][j + 1] == wbflag)
						&& (mGridArray[i + 2][j + 2] == wbflag)
						&& (mGridArray[i + 3][j + 3] == wbflag)
						&& (mGridArray[i + 4][j + 4] == wbflag)) {
					Log.e("check win or loss:", wbflag + "win");
					mWinFlag = wbflag;
				}
				// 右上到左下5个相连
				if (((i - 4) >= 0) && ((j + 4) < (GRID_SIZE - 1))
						&& (mGridArray[i][j] == wbflag)
						&& (mGridArray[i - 1][j + 1] == wbflag)
						&& (mGridArray[i - 2][j + 2] == wbflag)
						&& (mGridArray[i - 3][j + 3] == wbflag)
						&& (mGridArray[i - 4][j + 4] == wbflag)) {
					Log.e("check win or loss:", wbflag + "win");
					mWinFlag = wbflag;
				}
			}
		if (mWinFlag == wbflag) {
			return true;
		} else
			return false;
	}
	public boolean checkFull() {
		int mNotEmpty = 0;
		for (int i = 0; i < GRID_SIZE - 1; i++)
			for (int j = 0; j < GRID_SIZE - 1; j++) {
				if (mGridArray[i][j] != 0)
					mNotEmpty += 1;
			}
		if (mNotEmpty == (GRID_SIZE - 1) * (GRID_SIZE - 1))
			return true;
		else
			return false;
	}
	public void showTextView(CharSequence mT) {
		this.mStatusTextView.setText(mT);
		mStatusTextView.setVisibility(View.VISIBLE);

	}
}
```
这个看上去代码很多，但是我们来一点一点的分析你就会发现，原来不是很难的东西。大家第一要知道的就是用到了这些Bitmap，Canvas，Paint，Drawable，KeyEvent属性，这样我们就可以往下面介绍了。现在我们就是设置棋盘在界面上显示了。还有在声明的时候大家要是不喜欢用静态的也可以，这个是开发者自己定的。我们在设置两个数组，一个是棋子的，一个是棋盘的。public TextView mStatusTextView;根据游戏状态设置显示的文字，下面就是开发者怎么样自定义游戏的规则这个我就不多讲了。
我们怎么样把棋子放在中间呢，我用了一个mStartX = w / 2 - GRID_SIZE * GRID_WIDTH / 2;和mStartY = h / 2 - GRID_SIZE * GRID_WIDTH / 2;
这个其实说白了就是算坐标的问题，算好了x和y轴上的坐标就好了，最后就是当判断了，当黑棋赢了怎么办，当白旗赢了这么办，当棋盘被下满了这么办，这个也是开发者自己来定义吧，其实这个游戏就是这个思路。没有什么太难的。希望我介绍的思路能对大家有帮助。如果解释的不对，希望大家来指点指点。