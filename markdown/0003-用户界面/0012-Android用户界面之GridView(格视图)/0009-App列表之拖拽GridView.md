根据前面文章中ListView拖拽的实现原理,我们也是很容易实现推拽GridView的,下面我就以相同步骤实现基本的GridView拖拽效果。因为GridView不用做分组处理,代码处理起来更简洁,而且原理前面已经讲解清楚了,代码中只是简单的过下,必要的地方简单的注释一下。
1.主界面DragGridActivity.
```  
public class DragGridActivity extends Activity {
	private static List<String> list = null;
	// 自定义适配器
	private DragGridAdapter adapter = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.drag_grid_activity);
		initData();
		// 后面用到的自定义GridView
		DragGridView dragGridView = (DragGridView) findViewById(R.id.drag_grid);
		adapter = new DragGridAdapter(this, list);
		dragGridView.setAdapter(adapter);
	}
	public void initData() {
		// 数据结果
		list = new ArrayList<String>();
		for (int i = 0; i < 12; i++) {
			list.add("grid_" + i % 12);
		}
	}
}
```
2.主界面UI布局drag_grid_activity.xml.
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns：android="http：//schemas.android.com/apk/res/android"
	android：orientation="vertical"
	android：layout_width="fill_parent"
	android：layout_height="fill_parent"
	android：background="ffffff"
	android：padding="10dip"
	>
	<com.fengjian.test.DragGridView
		android：id="@+id/drag_grid"
		android：layout_width="fill_parent"
		android：layout_height="wrap_content"
		android：cacheColorHint="00000000"
		android：numColumns="3"
		android：stretchMode="columnWidth"
		android：verticalSpacing="5dip"
		android：horizontalSpacing="20dip"
		android：background="ffffff"/>
</LinearLayout>
```
3.列表项布局drag_grid_item.xml.
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns：android="http：//schemas.android.com/apk/res/android"
	android：layout_width="fill_parent"
	android：layout_height="wrap_content"
	android：paddingLeft="5dip"
	android：paddingRight="5dip">
	<ImageView android：id="@+id/drag_grid_item_image"
		android：src="@drawable/grid_icon"
		android：layout_margin="5dip"
		android：layout_alignParentTop="true
		android：layout_width="fill_parent"
		android：layout_height="wrap_content"/>
	<ImageView android：id="@+id/drag_grid_item_drag"
		android：src="@drawable/grid_drag"
		android：layout_alignParentTop="true
		android：layout_alignParentRight="true
		android：layout_width="wrap_content"
		android：layout_height="wrap_content"/>
</RelativeLayout>
```
4.自定义适配器DragGridAdapter,继承ArrayAdapter<String>
```  
public static class DragGridAdapter extends ArrayAdapter<String> {
	public DragGridAdapter(Context context, List<String> objects) {
		super(context, 0, objects);
	}
	public List<String> getList() {
		return list;
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		View view = convertView;
		if (view == null) {
			view = LayoutInflater.from(getContext()).inflate(
					R.layout.drag_grid_item, null);
		}
		try {
			// 根据文件名获取资源文件夹中的图片资源
			Field f = (Field) R.drawable.class
					.getDeclaredField(getItem(position));
			int i = f.getInt(R.drawable.class);
			ImageView imageview = (ImageView) view
					.findViewById(R.id.drag_grid_item_image);
			imageview.setImageResource(i);
		} catch (SecurityException e) {
			e.printStackTrace();
		} catch (NoSuchFieldException e) {
			e.printStackTrace();
		} catch (IllegalArgumentException e) {
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		}
		return view;
	}
}
```
5.自定义视图类DragGridView,继承GridView
```  
public class DragGridView extends GridView {
	// 定义基本的成员变量
	private ImageView dragImageView;
	private int dragSrcPosition;
	private int dragPosition;
	// x,y坐标的计算
	private int dragPointX;
	private int dragPointY;
	private int dragOffsetX;
	private int dragOffsetY;
	private WindowManager windowManager;
	private WindowManager.LayoutParams windowParams;
	private int scaledTouchSlop;
	private int upScrollBounce;
	private int downScrollBounce;
	public DragGridView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
```
6. 重写触控拦截事件方法onInterceptTouchEvent().
```  
@Override
public boolean onInterceptTouchEvent(MotionEvent ev) {
	if (ev.getAction() == MotionEvent.ACTION_DOWN) {
		int x = (int) ev.getX();
		int y = (int) ev.getY();
		dragSrcPosition = dragPosition = pointToPosition(x, y);
		if (dragPosition == AdapterView.INVALID_POSITION) {
			return super.onInterceptTouchEvent(ev);
		}
		ViewGroup itemView = (ViewGroup) getChildAt(dragPosition
				- getFirstVisiblePosition());
		dragPointX = x - itemView.getLeft();
		dragPointY = y - itemView.getTop();
		dragOffsetX = (int) (ev.getRawX() - x);
		dragOffsetY = (int) (ev.getRawY() - y);
		View dragger = itemView.findViewById(R.id.drag_grid_item_drag);
		// 如果选中拖动图标
		if (dragger != null && dragPointX > dragger.getLeft()
				&& dragPointX < dragger.getRight()
				&& dragPointY > dragger.getTop()
				&& dragPointY < dragger.getBottom() + 20) {
			upScrollBounce = Math.min(y - scaledTouchSlop, getHeight() / 4);
			downScrollBounce = Math.max(y + scaledTouchSlop,
					getHeight() * 3 / 4);
			itemView.setDrawingCacheEnabled(true);
			Bitmap bm = Bitmap.createBitmap(itemView.getDrawingCache());
			startDrag(bm, x, y);
		}
		return false;
	}
	return super.onInterceptTouchEvent(ev);
}
public void startDrag(Bitmap bm, int x, int y) {
	stopDrag();
	windowParams = new WindowManager.LayoutParams();
	windowParams.gravity = Gravity.TOP | Gravity.LEFT;
	windowParams.x = x - dragPointX + dragOffsetX;
	windowParams.y = y - dragPointY + dragOffsetY;
	windowParams.width = WindowManager.LayoutParams.WRAP_CONTENT;
	windowParams.height = WindowManager.LayoutParams.WRAP_CONTENT;
	windowParams.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE
			| WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE
			| WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON
			| WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN;
	windowParams.format = PixelFormat.TRANSLUCENT;
	windowParams.windowAnimations = 0;
	ImageView imageView = new ImageView(getContext());
	imageView.setImageBitmap(bm);
	windowManager = (WindowManager) getContext().getSystemService("window");
	windowManager.addView(imageView, windowParams);
	dragImageView = imageView;
}
public void onDrag(int x, int y) {
	if (dragImageView != null) {
		windowParams.alpha = 0.8f;
		windowParams.x = x - dragPointX + dragOffsetX;
		windowParams.y = y - dragPointY + dragOffsetY;
		windowManager.updateViewLayout(dragImageView, windowParams);
	}
	int tempPosition = pointToPosition(x, y);
	if (tempPosition != INVALID_POSITION) {
		dragPosition = tempPosition;
	}
	// 滚动
	if (y < upScrollBounce || y > downScrollBounce) {
		// 使用setSelection来实现滚动
		setSelection(dragPosition);
	}
}
```  
7.重写onTouchEvent()方法.
```  
@Override
public boolean onTouchEvent(MotionEvent ev) {
	if (dragImageView != null && dragPosition != INVALID_POSITION) {
		int action = ev.getAction();
		switch (action) {
		case MotionEvent.ACTION_UP:
			int upX = (int) ev.getX();
			int upY = (int) ev.getY();
			stopDrag();
			onDrop(upX, upY);
			break;
		case MotionEvent.ACTION_MOVE:
			int moveX = (int) ev.getX();
			int moveY = (int) ev.getY();
			onDrag(moveX, moveY);
			break;
		default:
			break;
		}
		return true;
	}
	return super.onTouchEvent(ev);
}
```
其中onDrag方法如下：
```  
public void onDrag(int x, int y) {
	if (dragImageView != null) {
		windowParams.alpha = 0.8f;
		windowParams.x = x - dragPointX + dragOffsetX;
		windowParams.y = y - dragPointY + dragOffsetY;
		windowManager.updateViewLayout(dragImageView, windowParams);
	}
	int tempPosition = pointToPosition(x, y);
	if (tempPosition != INVALID_POSITION) {
		dragPosition = tempPosition;
	}
	// 滚动
	if (y < upScrollBounce || y > downScrollBounce) {
		// 使用setSelection来实现滚动
		setSelection(dragPosition);
	}
}
```
8.放下影像,数据更新.
在onDrop方法中实现：
```  
public void onDrop(int x, int y) {
	// 为了避免滑动到分割线的时候,返回-1的问题
	int tempPosition = pointToPosition(x, y);
	if (tempPosition != INVALID_POSITION) {
		dragPosition = tempPosition;
	}
	// 超出边界处理
	if (y < getChildAt(0).getTop()) {
		// 超出上边界
		dragPosition = 0;
	} else if (y > getChildAt(getChildCount() - 1).getBottom()
			|| (y > getChildAt(getChildCount() - 1).getTop() && x > getChildAt(
					getChildCount() - 1).getRight())) {
		// 超出下边界
		dragPosition = getAdapter().getCount() - 1;
	}
	// 数据交换
	if (dragPosition != dragSrcPosition && dragPosition > -1
			&& dragPosition < getAdapter().getCount()) {
		DragGridAdapter adapter = (DragGridAdapter) getAdapter();
		String dragItem = adapter.getItem(dragSrcPosition);
		adapter.remove(dragItem);
		adapter.insert(dragItem, dragPosition);
		Toast.makeText(getContext(), adapter.getList().toString(),
				Toast.LENGTH_SHORT).show();
	}
}
```