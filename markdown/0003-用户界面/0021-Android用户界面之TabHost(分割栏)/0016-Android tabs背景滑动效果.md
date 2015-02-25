我们先来看看效果图，这样大家看了以后，就会做的它是怎么实现的。效果是什么样的。
![img](P)  
我们在来看看代码：
```  
public class SlipTabAct extends Activity {
	final int SUM = 3;
	TextView[] mTVs;
	ImageView[] mBGs;
	int mPreClickID = 0;
	int mCurClickID = 0;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		initView();
	}
	public void initView() {
		mTVs = new TextView[SUM];
		mTVs[0] = (TextView) this.findViewById(R.id.t1);
		mTVs[1] = (TextView) this.findViewById(R.id.t2);
		mTVs[2] = (TextView) this.findViewById(R.id.t3);
		mBGs = new ImageView[SUM];
		mBGs[0] = (ImageView) this.findViewById(R.id.b1);
		mBGs[1] = (ImageView) this.findViewById(R.id.b2);
		mBGs[2] = (ImageView) this.findViewById(R.id.b3);
		for (int i = 0; i < SUM; i++) {
			mTVs[i].setOnClickListener(clickListener);
		}
		mTVs[0].setEnabled(false);
		mPreClickID = 0;
	}
	private void updataCurView(int curClickID) {
		if (0 <= curClickID && SUM > curClickID) {
			mTVs[mPreClickID].setEnabled(true);
			mTVs[curClickID].setEnabled(false);
			mBGs[mPreClickID].setVisibility(View.INVISIBLE);
			mBGs[curClickID].setVisibility(View.VISIBLE);
			mPreClickID = curClickID;
		}
	}
	private void startSlip(View v) {
		Animation a = new TranslateAnimation(0.0f, v.getLeft()
				- mTVs[mPreClickID].getLeft(), 0.0f, 0.0f);
		a.setDuration(400);
		a.setFillAfter(false);
		a.setFillBefore(false);
		for (int i = 0; i < SUM; i++)
		{
			if (mTVs[i] == v) {
				mCurClickID = i;
				break;
			}
		}
		a.setAnimationListener(new AnimationListener() {
			public void onAnimationStart(Animation animation) {
			}
			public void onAnimationEnd(Animation animation) {
				updataCurView(mCurClickID);
			}
			public void onAnimationRepeat(Animation animation) {
			}
		});
		mBGs[mPreClickID].startAnimation(a);
	}
	private OnClickListener clickListener = new OnClickListener() {
		public void onClick(final View v) {
			startSlip(v);
		}
	};
}
```
我们从图就可以看出，这个是什么意思了，就是说，当我们点击或是指向到黄、智、远，这三个字的时候，就会出现一个背景，就是这么一个滑动效果，这个效果要怎么加在你的应用当中，这就看个人了。我们来看看这个是怎么样实现的。我们声明几个变量，完事我们再得到这些。定义好的变量。我们先用一个for循环，mTVs.setOnClickListener(clickListener);还要写上，再往下就是判断了，我们把判断的条件写成0<=curClickID&&SUM>curClickID记住的是，这两个里面要false。a.setFillAfter(false);a.setFillBefore(false);。这些都写完了，我们就要收尾了，收尾的时候要写上startSlip(v);括号里要写上v。这个一般人会给忘记。