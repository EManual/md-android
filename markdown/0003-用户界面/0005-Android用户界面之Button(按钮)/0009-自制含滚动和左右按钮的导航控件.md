最近帮一朋友做了一个可以滚动的导航，其实就是一个scrollView和一个gridview再加几个imageview的事，挺简单的，主要就是控制左右箭头显示与消失有点难，我是通过一个线程监听，来实现是否显示.
有以下功能点：
1.当用户点左右箭时，导航会左右滚
2.当滚到头时，对应的箭会消失
3.当用户点导航中显示不全的按钮时，被点中的按钮会自动滚到显示区
4.当用户在导航中滑动时，导航是会跟着滑动的
5.该应用是个控件，可以直接放到各种需要用的地方
先上图吧：
![img](http://emanual.github.io/md-android/img/view_button/10_button.png)
![img](http://emanual.github.io/md-android/img/view_button/10_button2.png)
![img](http://emanual.github.io/md-android/img/view_button/10_button3.png)
上代码吧：
```  
import java.util.ArrayList;
import android.content.Context;
import android.content.res.TypedArray;
import android.os.Handler;
import android.os.HandlerThread;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.MotionEvent;
import android.view.View;
import android.view.ViewGroup;
import android.view.View.OnClickListener;
import android.view.View.OnTouchListener;
import android.widget.BaseAdapter;
import android.widget.Button;
import android.widget.GridView;
import android.widget.HorizontalScrollView;
import android.widget.ImageView;
import android.widget.LinearLayout;
public class NavigationView extends LinearLayout {
	Context context;
	ImageView left;
	ImageView right;
	GridView gridView;
	LinearLayout layout;
	NavigationAdapter adapter;
	HorizontalScrollView scrollView;
	HandlerThread handlerThread;
	Handler listenHandler;
	Handler mHandler;
	int numCoum;
	NavigationListener navigationListener;
	public void setNavigationListener(NavigationListener navigationListener) {
		this.navigationListener = navigationListener;
	}
	public NavigationView(Context context, AttributeSet attrs) {
		super(context, attrs);
		this.context = context;
		initHandler();
		init(context);
		setupViews();
		initViews(attrs);
		setListener();

	}
	public void addNavigationCell(int id, int requestCode) {
		if (adapter.imgList.size() > numCoum) {
			throw new NavigationException("the numCoum Maximum value is :
					+ numCoum + " please check the number you have added!");
		} else {
			int[] obj = new int[] { id, requestCode };
			adapter.imgList.add(obj);
		}
	}
	private void setListener() {
		left.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				// scrollView.fling(-500);
				scrollView.arrowScroll(View.FOCUS_LEFT);
				updateLeftAndRightButton();
			}
		});
		right.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				// scrollView.fling(500);
				scrollView.arrowScroll(View.FOCUS_RIGHT);
				updateLeftAndRightButton();
			}
		});
		left.setVisibility(View.INVISIBLE);
		scrollView.setOnTouchListener(new OnTouchListener() {
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				if (event.getAction() == MotionEvent.ACTION_UP) {
					updateLeftAndRightButton();
				}
				return false;
			}
		});
	}
	private void initHandler() {
		mHandler = new Handler();
		handlerThread = new HandlerThread("listen scroll end");
		handlerThread.start();
		listenHandler = new Handler(handlerThread.getLooper());
	}
	private void init(Context context) {
		LayoutInflater inflater = LayoutInflater.from(context);
		layout = (LinearLayout) inflater.inflate(R.layout.main, null);
		LayoutParams layoutParam = new LinearLayout.LayoutParams(
				LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT);
		addView(layout, layoutParam);
	}
	void setupViews() {
		left = (ImageView) layout.findViewById(R.id.left);
		right = (ImageView) layout.findViewById(R.id.right);
		gridView = (GridView) layout.findViewById(R.id.list);
		scrollView = (HorizontalScrollView) layout
				.findViewById(R.id.scrollview);
	}
	void initViews(AttributeSet attrs) {
		TypedArray type = context.obtainStyledAttributes(attrs,
				R.styleable.NavigationView);
		float leftWidth = type.getDimension(
				R.styleable.NavigationView_left_width, 20f);
		float rightWidth = type.getDimension(
				R.styleable.NavigationView_right_width, 20f);
		float listWidth = type.getDimension(
				R.styleable.NavigationView_list_width, 100f);
		float scrollViewWidth = type.getDimension(
				R.styleable.NavigationView_list_scale, 300f);
		left.setImageResource(R.drawable.navigationt_left_selector);
		right.setImageResource(R.drawable.navigationt_right_selector);
		// Drawable leftdraw = type
		// .get(R.styleable.NavigationView_left_drawable);
		// left.ImageR(leftdraw);
		left.getLayoutParams().width = (int) leftWidth;
		right.getLayoutParams().width = (int) rightWidth;
		gridView.getLayoutParams().width = (int) listWidth;
		scrollView.getLayoutParams().width = (int) scrollViewWidth;
		numCoum = type.getInteger(R.styleable.NavigationView_navigation_size, 5);
		gridView.setNumColumns(numCoum);
		try {
			adapter = new NavigationAdapter(context, numCoum);
		} catch (IllegalArgumentException e) {
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		}
		gridView.setAdapter(adapter);
	}
	void updateLeftAndRightButton() {
		System.out.println("update");
		listenHandler.post(new Runnable() {
			int lastScrollX;
			@Override
			public void run() {
				System.out.println("lastScrollX" + lastScrollX);
				while (true) {
					lastScrollX = scrollView.getScrollX();
					try {
						Thread.sleep(50);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					System.out.println(scrollView.getScrollX() == lastScrollX);
					if (scrollView.getScrollX() == lastScrollX) {
						endScroll();
						return;
					}
				}
			}
		});
	}
	private void endScroll() {
		System.out.println("over");
		mHandler.post(new Runnable() {
			@Override
			public void run() {
				int x = scrollView.getScrollX();
				System.out.println(x);
				if (x == 0) {
					left.setVisibility(View.INVISIBLE);
				} else {
					left.setVisibility(View.VISIBLE);
				}
				System.out.println(scrollView.getWidth());
				System.out.println(gridView.getWidth());
				if (x == gridView.getWidth() - scrollView.getWidth()) {
					right.setVisibility(View.INVISIBLE);
				} else {
					right.setVisibility(View.VISIBLE);
				}
			}
		});
	}
	 /**
	  * set left button resource
	  * @param id
	  */
	public void setLeftImageResource(int id) {
		left.setImageResource(id);
	}
	 /**
	  * set right button resource
	  * @param id
	  */
	public void setRightImageResource(int id) {
		right.setImageResource(id);
	}
	// System.out.println(leftWidth);
	// System.out.println(rightWidth);
	private class NavigationAdapter extends BaseAdapter {
		private Context mContext;
		private ArrayList<int[]> imgList = new ArrayList<int[]>();
		public NavigationAdapter(Context c, int size)
				throws IllegalArgumentException, IllegalAccessException {
			mContext = c;
		}
		@Override
		public int getCount() {
			return imgList.size();
		}
		@Override
		public Object getItem(int position) {
			return position;
		}
		@Override
		public long getItemId(int position) {
			return position;
		}
		@Override
		public View getView(final int position, View convertView,
				ViewGroup parent) {
			ImageView imageView = null;
			if (convertView == null) {
				imageView = new ImageView(mContext);
				convertView = imageView;
			} else {
				imageView = (ImageView) convertView;
			}
			imageView.setImageResource(imgList.get(position)[0]);
			imageView.setFocusable(true);
			imageView.setClickable(true);
			imageView.setOnClickListener(new OnClickListener() {
				@Override
				public void onClick(View v) {
					int left_x = v.getLeft();
					int right_x = v.getRight();
					System.out.println("left_x" + left_x);
					System.out.println("right_x" + right_x);
					System.out.println(scrollView.getScrollX());
					if (right_x - scrollView.getScrollX() > scrollView
							.getWidth()) {
						scrollView.arrowScroll(View.FOCUS_RIGHT);
					}
					if (left_x < scrollView.getScrollX()) {
						scrollView.arrowScroll(View.FOCUS_LEFT);
					}
					if (NavigationView.this.navigationListener != null) {
						navigationListener.onClick(imgList.get(position)[1]);
					}
					// System.out.println(left);
				}
			});
			// i.setText("a");
			// 从imgList取得图片ID
			return convertView;
		}
	}
	interface NavigationListener {
		void onClick(int requestCode);
	}
	class NavigationException extends RuntimeException {
		public NavigationException(String s) {
			super(s);
		}
	}
}
```
布局文件如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="horizontal" >
    <ImageView
        android:id="@+id/left"
        android:layout_width="50px"
        android:layout_height="wrap_content"
        android:clickable="true"
        android:focusable="true"
        android:text="L" >
    </ImageView>
    <HorizontalScrollView
        android:id="@+id/scrollview"
        android:layout_width="220px"
        android:layout_height="wrap_content"
        android:scrollbarAlwaysDrawHorizontalTrack="false"
        android:scrollbars="none" >
        <LinearLayout
            android:id="@+id/llPlayback"
            android:layout_width="800px"
            android:layout_height="wrap_content"
            android:orientation="horizontal" >
            <GridView
                android:id="@+id/list"
                android:layout_width="400px"
                android:layout_height="fill_parent"
                android:numColumns="5" >
            </GridView>
        </LinearLayout>
    </HorizontalScrollView>
    <ImageView
        android:id="@+id/right"
        android:layout_width="50px"
        android:layout_height="wrap_content"
        android:clickable="true"
        android:focusable="true"
        android:text="R" >
    </ImageView>
</LinearLayout>
```
另外我还自定义了一点属性
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
<declare-styleable name="NavigationView">
    <attr name="left_width" format="dimension"></attr>
    <attr name="right_width" format="dimension"></attr>
    <attr name="left_drawable" format="reference"></attr>
    <attr name="right_drawable" format="reference"></attr>
    <attr name="navigation_size" format="integer"></attr>
    <attr name="list_width" format="dimension"></attr>
    <attr name="list_scale" format="dimension"></attr>
</declare-styleable>
</resources>
```
demo代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<org.sunshine.study.NavigationView
	xmlns:navigation="http://schemas.android.com/apk/res/org.sunshine.study"
	xmlns:android="http://schemas.android.com/apk/res/android" android:id="@+id/navigation
	android:orientation="horizontal" android:layout_width="fill_parent"
	navigation:left_width="30dp" navigation:right_width="30dp"
	navigation:navigation_size="7" navigation:list_width="500dp"
	navigation:list_scale="260dp" android:layout_height="fill_parent">
</org.sunshine.study.NavigationView>
```
以下是一些selector的设置
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
	<item android:state_pressed="true" android:drawable="@drawable/navigation_cell_focus" />
	<item android:state_focused="true" android:drawable="@drawable/navigation_cell_focus" />
	<item android:drawable="@drawable/navigation_cell" />  <!--  按钮正常情况下 -->
</selector>
```
实现类：
```  
import org.sunshine.study.NavigationView.NavigationListener;
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.GridView;
import android.widget.ImageButton;
public class NavigationViewActivityTest extends Activity implements
		NavigationListener {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.navigation_test);
		NavigationView navigationView = (NavigationView) findViewById(R.id.navigation);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 0);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 1);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 2);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 3);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 4);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 5);
		navigationView.addNavigationCell(R.drawable.navigationt_cell_selector, 6);
		navigationView.setNavigationListener(this);
	}
	@Override
	public void onClick(int requestCode) {

		Log.i("onClick", "requestCode is " + requestCode);
	}
}
```
我同样把它写成一个控件，下次要用只要把它引过来就能用