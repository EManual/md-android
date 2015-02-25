大家看到卷轴视图就会有点乱，下面我们会让大家先看看xml代码，看看界面是怎么设置的，还有就是再来看看主要的代码。我们会给大家解析一下。那么我们还是先来看看布局的代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/ScrollView01"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:scrollbars="none" >
    <LinearLayout
        android:id="@+id/layout"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" >
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="TextView0" />
        <Button
            android:id="@+id/Button01"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="Button" />
    </LinearLayout>
</ScrollView>
```
我们看见了就是用到一个button和textview，也不是很难就不给大家多说了，我们现在来看看主要的代码吧：
```  
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.view.KeyEvent;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.ScrollView;
import android.widget.TextView;
public class ScrollView01 extends Activity {
	private LinearLayout mLayout;
	private ScrollView mScrollView;
	private final Handler mHandler = new Handler();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 线性布局
		mLayout = (LinearLayout) findViewById(R.id.layout);
		mScrollView = (ScrollView) findViewById(R.id.ScrollView01);
		Button button = (Button) findViewById(R.id.Button01);
		button.setOnClickListener(mClickListener);
		// 改变默认焦点切换
		button.setOnKeyListener(mAddButtonKeyListener);
	}
	// 当点击按钮时，增加一个TextView和Button
	private Button.OnClickListener mClickListener = new Button.OnClickListener() {
		private int mIndex = 1;
		@Override
		public void onClick(View arg0) {
			TextView mTextView = new TextView(ScrollView01.this);
			mTextView.setText("Text View" + mIndex);
			LinearLayout.LayoutParams p = new LinearLayout.LayoutParams(
					LinearLayout.LayoutParams.FILL_PARENT,
					LinearLayout.LayoutParams.WRAP_CONTENT);
			// 增加一个TextView到线性布局中
			mLayout.addView(mTextView, p);
			Button mButtonView = new Button(ScrollView01.this);
			mButtonView.setText("Button" + mIndex++);
			mLayout.addView(mButtonView, p);
			// 改变默认焦点切换
			mButtonView.setOnKeyListener(mNewButtonKeyListener);
			// 投递一个消息进行滚动
			mHandler.post(mScrollToBottom);
		}
	};
	// 新建后滚动到最后
	private Runnable mScrollToBottom = new Runnable() {
		public void run() {
			int off = mLayout.getMeasuredHeight() - mScrollView.getHeight();
			if (off > 0) {
				mScrollView.scrollTo(0, off);
			}
		}
	};
	// 滚动到最后回到头
	private View.OnKeyListener mNewButtonKeyListener = new View.OnKeyListener() {
		@Override
		public boolean onKey(View v, int keyCode, KeyEvent event) {
			if (keyCode == KeyEvent.KEYCODE_DPAD_DOWN
					&& v == mLayout.getChildAt(mLayout.getChildCount() - 1)) {
				findViewById(R.id.Button01).requestFocus();
				return true;
			}
			return false;
		}
	};
	// 滚动到头回到尾
	private View.OnKeyListener mAddButtonKeyListener = new Button.OnKeyListener() {
		@Override
		public boolean onKey(View v, int keyCode, KeyEvent event) {
			View viewToFoucus = null;
			if (event.getAction() == KeyEvent.ACTION_DOWN) {
				int iCount = mLayout.getChildCount();
				switch (keyCode) {
				case KeyEvent.KEYCODE_DPAD_UP:
					if (iCount > 0) {
						viewToFoucus = mLayout.getChildAt(iCount - 1);
					}
					break;
				case KeyEvent.KEYCODE_DPAD_DOWN:
					if (iCount) {
						viewToFoucus = mLayout.getChildAt(iCount + 1);
					}
					break;
				default:
					break;
				}
			}
			if (viewToFoucus != null) {
				viewToFoucus.requestFocus();
				return true;
			} else {
				return false;
			}
		}
	};
}
```
上面的代码主要用到了KeyEvent，View，ScrollView这三个。我们用到先行布局来呈现出Button，mScrollView，我们在设置一个private的Button监听，我们在设置一下textview，最后我们就用一个run()方法，当我们的效果滚到最后时，我们要判断一下怎么样才能往回滚。我们在判断一下当滚到头时在滚到最后部，这样来回循环。上面我解析的大家要是不明白的就回帖指出来，要是我解析不对的，就指出来，指出来以后我好改正。