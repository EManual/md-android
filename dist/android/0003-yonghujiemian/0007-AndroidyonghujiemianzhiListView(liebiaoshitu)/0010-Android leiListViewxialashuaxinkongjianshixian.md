Android下拉刷新，在目前好多应用被使用到。
比如微博，下拉刷新更多数据。
一般我们在运用的ListView，本身就实现了下拉获取更多数据。只是这个下拉刷新的操作时在listView拉到底端的监听。
对于ListView刷新，我们可以分为两种情况：
1.获取更多的数据，按服务器数据库时间顺序存储入情况，此刻我们是获取是显示在我们应用中的数据更早前的数据，这也是最常见的情况。
比如(微博获取更多信息，就是获取更多更早前的信息，然后动态的添加到已有的数据的下方)；
2.获取更多的最新的数据，其实还是一种获取更多的操作方式。但是这里主要考虑到用户的操作习惯了。一般，用户的操作习惯分这么两种
第一种，获取下一页，第二种，类似于网页的F5刷新，停留在当前页面的刷新。
ListView刷新其实类似于网页。如果没用下拉刷新，那么用户得将ListView拖拉到最后(当然也可以是在界面顶端添加一个刷新按钮控件，但是，对于手机这样界面不是很大，这样的设计其实是不应太多的。)，如果数据太多，那么用户要下拉到很下面才能执行刷新。而对于大多数用户习惯，获取更多的最新资讯后，希望他添加的时候是在界面最上面的显眼处的。也就是，用户还是喜欢的是懒操作，在同一个可显示界面完成所有操作。那么，下拉刷新是一个不错的设计。
效果图：正常状态
![img](P)  
下拉刷新：
![img](P)  
基本效果就是这样。
自定义控件代码
```  
 /**
  * 刷新控制view
  */
public class RefreshableView extends LinearLayout {
	private static final String TAG = "LILITH";
	private Scroller scroller;
	private View refreshView;
	private ImageView refreshIndicatorView;
	private int refreshTargetTop = -60;
	private ProgressBar bar;
	private TextView downTextView;
	private TextView timeTextView;
	private RefreshListener refreshListener;
	private String downTextString;
	private String releaseTextString;
	private Long refreshTime = null;
	private int lastX;
	private int lastY;
	// 拉动标记
	private boolean isDragging = false;
	// 是否可刷新标记
	private boolean isRefreshEnabled = true;
	// 在刷新中标记
	private boolean isRefreshing = false;
	private Context mContext;
	public RefreshableView(Context context) {
		super(context);
		mContext = context;
	}
	public RefreshableView(Context context, AttributeSet attrs) {
		super(context, attrs);
		mContext = context;
		init();
	}
	private void init() {
		// 滑动对象，
		scroller = new Scroller(mContext);
		// 刷新视图顶端的的view
		refreshView = LayoutInflater.from(mContext).inflate(
				R.layout.refresh_top_item, null);
		// 指示器view
		refreshIndicatorView = (ImageView) refreshView
				.findViewById(R.id.indicator);
		// 刷新bar
		bar = (ProgressBar) refreshView.findViewById(R.id.progress);
		// 下拉显示text
		downTextView = (TextView) refreshView.findViewById(R.id.refresh_hint);
		// 下来显示时间
		timeTextView = (TextView) refreshView.findViewById(R.id.refresh_time);

		LayoutParams lp = new LinearLayout.LayoutParams(
				LayoutParams.FILL_PARENT, -refreshTargetTop);
		lp.topMargin = refreshTargetTop;
		lp.gravity = Gravity.CENTER;
		addView(refreshView, lp);
		downTextString = mContext.getResources().getString(
				R.string.refresh_down_text);
		releaseTextString = mContext.getResources().getString(
				R.string.refresh_release_text);
	}
	 /**
	  * 刷新
	  * @param time
	  */
	private void setRefreshText(String time) {
		// timeTextView.setText(time);
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		int y = (int) event.getRawY();
		switch (event.getAction()) {
		case MotionEvent.ACTION_DOWN:
			// 记录下y坐标
			lastY = y;
			break;
		case MotionEvent.ACTION_MOVE:
			Log.i(TAG, "ACTION_MOVE");
			// y移动坐标
			int m = y - lastY;
			if (((m < 6) && (m > -1)) || (!isDragging)) {
				doMovement(m);
			}
			// 记录下此刻y坐标
			this.lastY = y;
			break;
		case MotionEvent.ACTION_UP:
			Log.i(TAG, "ACTION_UP");
			fling();
			break;
		}
		return true;
	}
	 /**
	  * up事件处理
	  */
	private void fling() {
		LinearLayout.LayoutParams lp = (LayoutParams) refreshView
				.getLayoutParams();
		Log.i(TAG, "fling()" + lp.topMargin);
		if (lp.topMargin > 0) {// 拉到了触发可刷新事件
			refresh();
		} else {
			returnInitState();
		}
	}
	private void returnInitState() {
		LinearLayout.LayoutParams lp = (LinearLayout.LayoutParams) this.refreshView
				.getLayoutParams();
		int i = lp.topMargin;
		scroller.startScroll(0, i, 0, refreshTargetTop);
		invalidate();
	}
	private void refresh() {
		LinearLayout.LayoutParams lp = (LinearLayout.LayoutParams) this.refreshView
				.getLayoutParams();
		int i = lp.topMargin;
		refreshIndicatorView.setVisibility(View.GONE);
		bar.setVisibility(View.VISIBLE);
		timeTextView.setVisibility(View.GONE);
		downTextView.setVisibility(View.GONE);
		scroller.startScroll(0, i, 0, 0 - i);
		invalidate();
		if (refreshListener != null) {
			refreshListener.onRefresh(this);
			isRefreshing = true;
		}
	}
	@Override
	public void computeScroll() {
		if (scroller.computeScrollOffset()) {
			int i = this.scroller.getCurrY();
			LinearLayout.LayoutParams lp = (LinearLayout.LayoutParams) this.refreshView
					.getLayoutParams();
			int k = Math.max(i, refreshTargetTop);
			lp.topMargin = k;
			this.refreshView.setLayoutParams(lp);
			this.refreshView.invalidate();
			invalidate();
		}
	}
	 /**
	  * 下拉move事件处理
	  * @param moveY
	  */
	private void doMovement(int moveY) {
		LinearLayout.LayoutParams lp = (LayoutParams) refreshView
				.getLayoutParams();
		if (moveY > 0) {
			// 获取view的上边距
			float f1 = lp.topMargin;
			float f2 = moveY * 0.3F;
			int i = (int) (f1 + f2);
			// 修改上边距
			lp.topMargin = i;
			// 修改后刷新
			refreshView.setLayoutParams(lp);
			refreshView.invalidate();
			invalidate();
		}
		timeTextView.setVisibility(View.VISIBLE);
		if (refreshTime != null) {
			setRefreshTime(refreshTime);
		}
		downTextView.setVisibility(View.VISIBLE);
		refreshIndicatorView.setVisibility(View.VISIBLE);
		bar.setVisibility(View.GONE);
		if (lp.topMargin > 0) {
			downTextView.setText(R.string.refresh_release_text);
			refreshIndicatorView.setImageResource(R.drawable.refresh_arrow_up);
		} else {
			downTextView.setText(R.string.refresh_down_text);
			refreshIndicatorView.setImageResource(R.drawable.refresh_arrow_down);
		}
	}
	public void setRefreshEnabled(boolean b) {
		this.isRefreshEnabled = b;
	}
	public void setRefreshListener(RefreshListener listener) {
		this.refreshListener = listener;
	}
	 /**
	  * 刷新时间
	  * @param refreshTime2
	  */
	private void setRefreshTime(Long time) {
	}
	 /**
	  * 结束刷新事件
	  */
	public void finishRefresh() {
		Log.i(TAG, "执行了=====finishRefresh");
		LinearLayout.LayoutParams lp = (LinearLayout.LayoutParams) this.refreshView
				.getLayoutParams();
		int i = lp.topMargin;
		refreshIndicatorView.setVisibility(View.VISIBLE);
		timeTextView.setVisibility(View.VISIBLE);
		scroller.startScroll(0, i, 0, refreshTargetTop);
		invalidate();
		isRefreshing = false;
	}
	/*
	  * 该方法一般和ontouchEvent 一起用 (non-Javadoc) <a
	  * href="\"http://www.eoeandroid.com/home.php?mod=space&uid=133757\""
	  * target="\"_blank\"">@see</a>
	  * android.view.ViewGrouponInterceptTouchEvent(android.view.MotionEvent)
	  */
	@Override
	public boolean onInterceptTouchEvent(MotionEvent e) {
		int action = e.getAction();
		int y = (int) e.getRawY();
		switch (action) {
		case MotionEvent.ACTION_DOWN:
			lastY = y;
			break;
		case MotionEvent.ACTION_MOVE:
			// y移动坐标
			int m = y - lastY;
			// 记录下此刻y坐标
			this.lastY = y;
			if (m > 6 && canScroll()) {
				return true;
			}
			break;
		case MotionEvent.ACTION_UP:
			break;
		case MotionEvent.ACTION_CANCEL:
			break;
		}
		return false;
	}
	private boolean canScroll() {
		View childView;
		if (getChildCount() > 1) {
			childView = this.getChildAt(1);
			if (childView instanceof ListView) {
				int top = ((ListView) childView).getChildAt(0).getTop();
				int pad = ((ListView) childView).getListPaddingTop();
				if ((Math.abs(top - pad)) < 3
						&& ((ListView) childView).getFirstVisiblePosition() == 0) {
					return true;
				} else {
					return false;
				}
			} else if (childView instanceof ScrollView) {
				if (((ScrollView) childView).getScrollY() == 0) {
					return true;
				} else {
					return false;
				}
			}
		}
		return false;
	}
	 /**
	  * 刷新监听接口
	  */
	public interface RefreshListener {
		public void onRefresh(RefreshableView view);
	}
}
```
此控件自定义实现一个线性布局，内部包含一个第一个子控件，刷新显示的View。因为对于ListView下拉刷新的例子网上挺多的，上次我朋友也做，说特么弄了一个礼拜发现一个问题，listview里面条目太少时，刷新的view就会显示出来。我没具体看过那个代码，也不知道到底什么情况。自个定义的稍微用了点小技巧。即，我将刷新view的topMargin设置为了该view的高度的负数，那么，他就刚好隐藏起来了。对于上面那个对于条目太少而造成刷新的view显示的bug有所解决。 
```  
@Override
public boolean onTouchEvent(MotionEvent event) {
	int y = (int) event.getRawY();
	switch (event.getAction()) {
	case MotionEvent.ACTION_DOWN:
		// 记录下y坐标
		lastY = y;
		break;
	case MotionEvent.ACTION_MOVE:
		Log.i(TAG, "ACTION_MOVE");
		// y移动坐标
		int m = y - lastY;
		if (((m < 6) && (m > -1)) || (!isDragging)) {
			doMovement(m);
		}
		// 记录下此刻y坐标
		this.lastY = y;
		break;
	case MotionEvent.ACTION_UP:
		Log.i(TAG, "ACTION_UP");
		fling();
		break;
	}
	return true;
}
@Override
public boolean onInterceptTouchEvent(MotionEvent e) {
	int action = e.getAction();
	int y = (int) e.getRawY();
	switch (action) {
	case MotionEvent.ACTION_DOWN:
		lastY = y;
		break;
	case MotionEvent.ACTION_MOVE:
		// y移动坐标
		int m = y - lastY;
		// 记录下此刻y坐标
		this.lastY = y;
		if (m > 6 && canScroll()) {
			return true;
		}
		break;
	case MotionEvent.ACTION_UP:
		break;
	case MotionEvent.ACTION_CANCEL:
		break;
	}
	return false;
}
```
控件使用： 
```  
<pre name="code" class="html">
<com.xxx.xxxx.view.RefreshableView 
	android:orientation="vertical"
	android:id="@+id/refresh_root"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	xmlns:android="http://schemas.android.com/apk/res/android" >
	<ListView android:id="@+id/def_list" android:layout_width="fill_parent"
		android:scrollbars="none" android:fadingEdge="none"
		android:layout_height="wrap_content" android:scrollingCache="false">
	</ListView>
</com.xxxx.xxxxx.view.RefreshableView>
```
即，我的listview是以该控件的第二个子view加入进去。此刻又会遇到一个问题：我在界面执行下拉时，应用如何分辨我是执行的RefreshableView下拉刷新操作，还是listview下拉操作。这个问题一开始挺悲剧的，就是当我的listview中数据挺多，那我手势一网上滑，此刻操作不会出现冲突，因为刷新View监听的下滑事件监听；然后此刻listview滑到了底部，我要回到listview顶部去，就得执行向下滑操作，那么这时的操作就和刷新view中下滑，下拉刷新监听冲突。后来发现了view中两个函数：子控件截取事件函数。具体来说是就是用的点击屏幕，touch事件会先被onInterceptTouchEvent捕捉，子view先做处理，然后根据true和false，才判断是否截取事件交给父View处理。那么我们就可以在onInterceptTouchEvent做了预处理，更具判断listview是佛在最底部，可下滑，来判断是佛让子view来截取处理该touch事件。具体判断看canScroll()；同样的考虑到ScrollView类似有listview的情况，也做了处理，因此该控件支持包含listview和scrollview。实现代码基本如上，内部可能还有挺多的bug，但是目前我应用上实现暂没发现。最后感谢网上的可刷新代码demo，以及sina微博可刷新控件代码以及网易应用可刷新控件代码。 
```  
<pre name="code" class="html" style="background-color:rgb(255,255,255)">
```