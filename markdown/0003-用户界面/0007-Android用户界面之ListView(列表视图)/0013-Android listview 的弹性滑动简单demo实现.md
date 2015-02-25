弹性滑动的效果比较好看。但是 在2.2以下版本中，android 本身没有实现，想要实现这中效果要自己去写
1.自己些一个MyListview  继承listview 类：
```  
import android.content.Context;
import android.graphics.Rect;
import android.util.AttributeSet;
import android.util.Log;
import android.view.GestureDetector;
import android.view.MotionEvent;
import android.view.View;
import android.view.GestureDetector.OnGestureListener;
import android.view.animation.TranslateAnimation;
import android.widget.ListView;
public class MyListView extends ListView {
	private Context context;
	private boolean outBound = false;
	private int distance;
	private int firstOut;
	private static final String TAG = "wljie";
	public MyListView(Context context, AttributeSet attrs) {
		super(context, attrs);
		this.context = context;
		Log.d(TAG, "IN 1");
	}
	public MyListView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		this.context = context;
		Log.d(TAG, "IN 2");
	}
	public MyListView(Context context) {
		super(context);
		this.context = context;
		Log.d(TAG, "IN 3");
	}
	GestureDetector gestureDetector = new GestureDetector(
			new OnGestureListener() {
				@Override
				public boolean onSingleTapUp(MotionEvent e) {
					return false;
				}
				@Override
				public void onShowPress(MotionEvent e) {
				}
				[Tags]/**
				 [Tags]* 手势滑动的时候触发
				 [Tags]*/
				@Override
				public boolean onScroll(MotionEvent e1, MotionEvent e2,
						float distanceX, float distanceY) {
					Log.d(TAG, "ENTER onscroll");
					int firstPos = getFirstVisiblePosition();
					int lastPos = getLastVisiblePosition();
					int itemCount = getCount();
					// outbound Top
					if (outBound && firstPos != 0 && lastPos != (itemCount - 1)) {
						scrollTo(0, 0);
						return false;
					}
					View firstView = getChildAt(firstPos);
					if (!outBound)
						firstOut = (int) e2.getRawY();
					if (firstView != null
							&& (outBound || (firstPos == 0
									&& firstView.getTop() == 0 && distanceY < 0))) {
						// Record the length of each slide
						distance = firstOut - (int) e2.getRawY();
						scrollTo(0, distance / 2);
						return true;
					}
					// outbound Bottom
					return false;
				}
				@Override
				public void onLongPress(MotionEvent e) {
				}
				@Override
				public boolean onFling(MotionEvent e1, MotionEvent e2,
						float velocityX, float velocityY) {
					return false;
				}
				@Override
				public boolean onDown(MotionEvent e) {
					return false;
				}
			});
	@Override
	public boolean dispatchTouchEvent(MotionEvent event) {
		Log.d(TAG, "dispatchTouchEvent");
		int act = event.getAction();
		if ((act == MotionEvent.ACTION_UP || act == MotionEvent.ACTION_CANCEL)
				&& outBound) {
			outBound = false;
			// scroll back
		}
		if (!gestureDetector.onTouchEvent(event)) {
			outBound = false;
		} else {
			outBound = true;
		}
		Rect rect = new Rect();
		getLocalVisibleRect(rect);
		TranslateAnimation am = new TranslateAnimation(0, 0, -rect.top, 0);
		am.setDuration(300);
		startAnimation(am);
		scrollTo(0, 0);
		return super.dispatchTouchEvent(event);
	}
}
```
2.在main.xml 中写入
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/LinearLayout01"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <com.wljie.MyListView
        android:id="@+id/MyListView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>
```
3.创建一个my_listitem.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/MyListItem"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:paddingBottom="3dip"
    android:paddingLeft="10dip" >
    <TextView
        android:id="@+id/ItemTitle"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:textSize="30dip" >
    </TextView>
    <TextView
        android:id="@+id/ItemText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
    </TextView>
</LinearLayout>
```
4.在Main.java中进行下一步的显示
```  
import java.util.ArrayList;
import java.util.HashMap;
import android.app.Activity;
import android.graphics.Rect;
import android.os.Bundle;
import android.view.animation.TranslateAnimation;
import android.widget.SimpleAdapter;
public class Main extends Activity {
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 绑定XML中的ListView，作为Item的容器
		MyListView list = (MyListView) findViewById(R.id.MyListView);
		// 生成动态数组，并且转载数据
		ArrayList<HashMap<String, String>> mylist = new ArrayList<HashMap<String, String>>();
		for (int i = 0; i < 30; i++) {
			HashMap<String, String> map = new HashMap<String, String>();
			map.put("ItemTitle", "This is Title.....");
			map.put("ItemText", "This is text.....");
			mylist.add(map);
		}
		// 生成适配器，数组===》ListItem
		SimpleAdapter mSchedule = new SimpleAdapter(this, // 没什么解释
				mylist,// 数据来源
				R.layout.my_listitem,// ListItem的XML实现
				// 动态数组与ListItem对应的子项
				new String[] { "ItemTitle", "ItemText" },
				// ListItem的XML文件里面的两个TextView ID
				new int[] { R.id.ItemTitle, R.id.ItemText });
		// 添加并且显示
		list.setAdapter(mSchedule);
	}
}
```