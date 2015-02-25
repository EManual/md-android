Android系统的进度条控件默认的设计的不是很周全，比如没有包含文字的显示，那么如何在Android进度条控件上显示文字呢? 来自Google内部的代码来了解下，主要使用的addView这样的方法通过覆盖一层Chronometer秒表控件来实现，整个代码如下
```  
public class TextProgressBar extends RelativeLayout implements
		OnChronometerTickListener {
	public static final String TAG = "TextProgressBar";
	static final int CHRONOMETER_ID = android.R.id.text1;
	static final int PROGRESSBAR_ID = android.R.id.progress;
	Chronometer mChronometer = null;
	ProgressBar mProgressBar = null;
	long mDurationBase = -1;
	int mDuration = -1;
	boolean mChronometerFollow = false;
	int mChronometerGravity = Gravity.NO_GRAVITY;
	public TextProgressBar(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	public TextProgressBar(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public TextProgressBar(Context context) {
		super(context);
	}
	// <a href="\"http://www.eoeandroid.com/\"" target="\"_blank\""
	// class="\"relatedlink\"">Android开发</a>网提示关键部分在这里
	@Override
	public void addView(View child, int index, ViewGroup.LayoutParams params) {
		super.addView(child, index, params);
		int childId = child.getId();
		if (childId == CHRONOMETER_ID && child instanceof Chronometer) {
			mChronometer = (Chronometer) child;
			mChronometer.setOnChronometerTickListener(this);
			// Check if Chronometer should move with with ProgressBar
			mChronometerFollow = (params.width == ViewGroup.LayoutParams.WRAP_CONTENT);
			mChronometerGravity = (mChronometer.getGravity() & Gravity.HORIZONTAL_GRAVITY_MASK);
		} else if (childId == PROGRESSBAR_ID && child instanceof ProgressBar) {
			mProgressBar = (ProgressBar) child;
		}
	}
	@android.view.RemotableViewMethod
	public void setDurationBase(long durationBase) {
		mDurationBase = durationBase;
		if (mProgressBar == null || mChronometer == null) {
			throw new RuntimeException(
					"Expecting child ProgressBar with id 
							+ "'android.R.id.progress' and Chronometer id 'android.R.id.text1'");
		}
		// Update the ProgressBar maximum relative to Chronometer base
		mDuration = (int) (durationBase - mChronometer.getBase());
		if (mDuration <= 0) {
			mDuration = 1;
		}
		mProgressBar.setMax(mDuration);
	}
	public void onChronometerTick(Chronometer chronometer) {
		if (mProgressBar == null) {
			throw new RuntimeException(
					"Expecting child ProgressBar with id 'android.R.id.progress'");
		}
		// Stop Chronometer if we're past duration
		long now = SystemClock.elapsedRealtime();
		if (now >= mDurationBase) {
			mChronometer.stop();
		}
		int remaining = (int) (mDurationBase - now);
		mProgressBar.setProgress(mDuration - remaining);
		if (mChronometerFollow) {
			RelativeLayout.LayoutParams params;
			params = (RelativeLayout.LayoutParams) mProgressBar.getLayoutParams();
			int contentWidth = mProgressBar.getWidth() - (params.leftMargin + params.rightMargin);
			int leadingEdge = ((contentWidth * mProgressBar.getProgress()) / mProgressBar.getMax()) + params.leftMargin;
			int adjustLeft = 0;
			int textWidth = mChronometer.getWidth();
			if (mChronometerGravity == Gravity.RIGHT) {
				adjustLeft = -textWidth;
			} else if (mChronometerGravity == Gravity.CENTER_HORIZONTAL) {
				adjustLeft = -(textWidth / 2);
			}
			leadingEdge += adjustLeft;
			int rightLimit = contentWidth - params.rightMargin - textWidth;
			if (leadingEdge < params.leftMargin) {
				leadingEdge = params.leftMargin;
			} else if (leadingEdge > rightLimit) {
				leadingEdge = rightLimit;
			}
			params = (RelativeLayout.LayoutParams) mChronometer.getLayoutParams();
			params.leftMargin = leadingEdge;
			mChronometer.requestLayout();
		}
	}
}
```