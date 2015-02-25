由于ViewFlipper在外，ScrollView在内，因此一般的做法是定义一个手势响应类来处理响应事件，并将响应事件的处理交给内层的ScrollView。大致代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.GestureDetector;
import android.view.GestureDetector.SimpleOnGestureListener;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MotionEvent;
import android.view.View;
import android.view.Window;
import android.widget.TextView;
import android.widget.Toast;
import android.widget.ViewFlipper;
public class Test1 extends Activity {
	private ViewFlipper viewFlipper;
	private String[] descriptionsArray;
	private String[] titleArray;
	private int selectedPosition;
	private TextView textViewTitle;
	private TextView textViewContent;
	private FriendlyScrollView scroll;
	private LayoutInflater mInflater;
	private GestureDetector gestureDetector;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.common_info_list_view);
		InitUI();
		super.onCreate(savedInstanceState);
		Toast.makeText(this, R.string.hello, Toast.LENGTH_SHORT).show();
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		super.onCreateOptionsMenu(menu);
		return false;
	}
	@Override
	public void onBackPressed() {
		finish();
	}
	private void InitUI() {
		viewFlipper = (ViewFlipper) findViewById(R.id.viewflipper_data);
		mInflater = LayoutInflater.from(this);
		fillDate();
		viewFlipper.addView(getContentView());
	}
	private void fillDate() {
		selectedPosition = 0;
		titleArray = getResources().getStringArray(R.array.title_array);
		descriptionsArray = getResources().getStringArray(R.array.description_array);
		gestureDetector = new GestureDetector(new CommonGestureListener());
	}
	private View getContentView() {
		View contentView = new View(this);
		contentView = mInflater.inflate(R.layout.common_info_item_view, null);
		textViewTitle = (TextView) contentView.findViewById(R.id.text_title);
		textViewContent = (TextView) contentView.findViewById(R.id.text_detail);
		textViewTitle.setText(titleArray[selectedPosition]);
		textViewTitle.setPadding(10, 0, 10, 0);
		textViewContent.setText(descriptionsArray[selectedPosition]);
		textViewContent.setPadding(10, 5, 10, 5);
		scroll = (FriendlyScrollView) contentView.findViewById(R.id.scroll);
		scroll.setOnTouchListener(onTouchListener);
		scroll.setGestureDetector(gestureDetector);
		return contentView;
	}
	private View.OnTouchListener onTouchListener = new View.OnTouchListener() {
		public boolean onTouch(View v, MotionEvent event) {
			return gestureDetector.onTouchEvent(event);
		}
	};
	public class CommonGestureListener extends SimpleOnGestureListener {
		@Override
		public boolean onDown(MotionEvent e) {
			Log.d("QueryViewFlipper", "====> Jieqi: do onDown...");
			return false;
		}
		@Override
		public void onShowPress(MotionEvent e) {
			Log.d("QueryViewFlipper", "====> Jieqi: do onShowPress...");
			super.onShowPress(e);
		}
		@Override
		public void onLongPress(MotionEvent e) {
			Log.d("QueryViewFlipper", "----> Jieqi: do onLongPress...");
		}
		@Override
		public boolean onSingleTapConfirmed(MotionEvent e) {
			Log.d("QueryViewFlipper", "====> Jieqi: do onSingleTapConfirmed...");
			return false;
		}
		@Override
		public boolean onSingleTapUp(MotionEvent e) {
			Log.d("QueryViewFlipper", "====> Jieqi: do onSingleTapUp...");
			return false;
		}
		@Override
		public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
				float velocityY) {
			Log.d("QueryViewFlipper", "====> Jieqi: do onFling...");
			if (e1.getX() - e2.getX() > 100 && Math.abs(velocityX) > 50) {
				// 向左
				selectedPosition = selectedPosition + 1 < titleArray.length ? (selectedPosition + 1)
						: 0;
				viewFlipper.addView(getContentView());
				viewFlipper.setInAnimation(AnimationControl
						.inFromRightAnimation());
				viewFlipper.setOutAnimation(AnimationControl
						.outToLeftAnimation());
				viewFlipper.showNext();
				viewFlipper.removeViewAt(0);
			} else if (e2.getX() - e1.getX() > 100 && Math.abs(velocityX) > 50) {
				// 向右
				selectedPosition = selectedPosition > 0 ? (selectedPosition - 1)
						: (titleArray.length - 1);
				viewFlipper.addView(getContentView());
				viewFlipper.setInAnimation(AnimationControl
						.inFromLeftAnimation());
				viewFlipper.setOutAnimation(AnimationControl
						.outToRightAnimation());
				viewFlipper.showNext();
				viewFlipper.removeViewAt(0);
			}
			return true;
		}
		@Override
		public boolean onScroll(MotionEvent e1, MotionEvent e2,
				float distanceX, float distanceY) {
			Log.d("QueryViewFlipper", "====> Jieqi: do onScroll...");
			return super.onScroll(e1, e2, distanceX, distanceY);
		}
	}
}
```
这个时候问题出现了，通过Log显示，当ScrollView中内容太短的时候，ScrollView不会触发OnScroll和OnFling事件，导致ViewFlipper左右滑动不响应。（当然后来的另一个测试表明这个问题在ListView上不存在）
为了解决这一个问题，我重新自定义了一个FriendlyScrollView类，来重写ScrollView的onTouchEvent和dispatchTouchEvent方法，具体如下：
```  
import android.content.Context;
import android.util.AttributeSet;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.widget.ScrollView;
public class FriendlyScrollView extends ScrollView {
	GestureDetector gestureDetector;
	public FriendlyScrollView(Context context) {
		super(context);
	}
	public FriendlyScrollView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public FriendlyScrollView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	public void setGestureDetector(GestureDetector gestureDetector) {
		this.gestureDetector = gestureDetector;
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		super.onTouchEvent(event);
		return gestureDetector.onTouchEvent(event);
	}
	@Override
	public boolean dispatchTouchEvent(MotionEvent ev) {
		gestureDetector.onTouchEvent(ev);
		super.dispatchTouchEvent(ev);
		return true;
	}
}
```
然后将common_info_view.xml和程序中的ScrollView改成FriendlyScrollView，终于解决了这个问题。