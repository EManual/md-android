这里定义了分组标签集合groupKey后面分组的时候会用到。
5.自定义适配器类DragListAdapter。
接着我们搭建数据适配器，负责把list的数据填充到ListView中。
```  
public static class DragListAdapter extends ArrayAdapter<String> {
	public DragListAdapter(Context context, List<String> objects) {
		super(context, 0, objects);
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		View view = convertView;
		if (view == null) {
			// 加载列表项模板
			view = LayoutInflater.from(getContext()).inflate(
					R.layout.drag_list_item, null);
		}
		TextView textView = (TextView) view
				.findViewById(R.id.drag_list_item_text);
		textView.setText(getItem(position));
		return view;
	}
}
```
注意getItem(position)会取得数组适配器中position位置的T（这里是字符串），比较好用的一个方法。至此，我们准备了一个正常的数据列表，效果如下：
二、实现
上面部分是我们的一个准备工作，接下来我们通过自定义ListView，重写ListView中onInterceptTouchEvent(),onTouchEvent()方法来响应触控事件做相应的界面调整(选中，拖动，数据更改后刷新界面)等等。
6.自定义视图类。
```  
//自定义ListView，准备改造成自己想要的ListView
//这样的好处是我们不仅可以直接使用ListView很多现成的稳定的方法，而且可以重写方法改写ListView的行为（利用的是java面向对象的继承特性，本人喜欢在任何代码中分析面向对象的特性、原则和模式）
public class DragListView extends ListView {
	private int scaledTouchSlop;// 判断滑动的一个距离,scroll的时候会用到
	public DragListView(Context context, AttributeSet attrs) {
		super(context, attrs);
		scaledTouchSlop = ViewConfiguration.get(context).getScaledTouchSlop();
	}
}
```
7.重写触控拦截事件方法onInterceptTouchEvent()。
为了能在子控件响应触摸事件的情况下此ListView也能监听到触摸事件，我们把重写这个方法，做一些初始化工作。我们在这里捕获down事件，在down事件中，我们做一些拖动的准备工作：
1）获取点击数据项，初始化一些变量；
2）判断是否是拖动还是仅仅点击；
3）如果是拖动，建立拖动影像；
这些工作是我们后面拖动的一个执行基础，非常重要。
```  
// 下面定义要使用的所有变量
private ImageView dragImageView;// 被拖拽项的影像，其实就是一个ImageView
private int dragSrcPosition;// 手指拖动项原始在列表中的位置
private int dragPosition;// 手指拖动的时候，当前拖动项在列表中的位置
private int dragPoint;// 在当前数据项中的位置
private int dragOffset;// 当前视图和屏幕的距离(这里只使用了y方向上)
private WindowManager windowManager;// windows窗口控制类
private WindowManager.LayoutParams windowParams;// 用于控制拖拽项的显示的参数
private int scaledTouchSlop;// 判断滑动的一个距离
private int upScrollBounce;// 拖动的时候，开始向上滚动的边界
private int downScrollBounce;// 拖动的时候，开始向下滚动的边界
@Override
public boolean onInterceptTouchEvent(MotionEvent ev) {
	// 捕获down事件
	if (ev.getAction() == MotionEvent.ACTION_DOWN) {
		int x = (int) ev.getX();
		int y = (int) ev.getY();
		// 选中的数据项位置，使用ListView自带的pointToPosition(x, y)方法
		dragSrcPosition = dragPosition = pointToPosition(x, y);
		// 如果是无效位置(超出边界，分割线等位置)，返回
		if (dragPosition == AdapterView.INVALID_POSITION) {
			return super.onInterceptTouchEvent(ev);
		}
		// 获取选中项View
		// getChildAt(int position)显示display在界面的position位置的View
		// getFirstVisiblePosition()返回第一个display在界面的view在adapter的位置position，可能是0，也可能是4
		ViewGroup itemView = (ViewGroup) getChildAt(dragPosition
				- getFirstVisiblePosition());

		// dragPoint点击位置在点击View内的相对位置
		// dragOffset屏幕位置和当前ListView位置的偏移量，这里只用到y坐标上的值
		// 这两个参数用于后面拖动的开始位置和移动位置的计算
		dragPoint = y - itemView.getTop();
		dragOffset = (int) (ev.getRawY() - y);

		// 获取右边的拖动图标，这个对后面分组拖拽有妙用
		View dragger = itemView.findViewById(R.id.drag_list_item_image);
		// 如果在右边位置（拖拽图片左边的20px的右边区域）
		if (dragger != null && x > dragger.getLeft() - 20) {
			// 准备拖动
			// 初始化拖动时滚动变量
			// scaledTouchSlop定义了拖动的偏差位(一般+-10)
			// upScrollBounce当在屏幕的上部(上面1/3区域)或者更上的区域，执行拖动的边界，downScrollBounce同理定义
			upScrollBounce = Math.min(y - scaledTouchSlop, getHeight() / 3);
			downScrollBounce = Math.max(y + scaledTouchSlop,
					getHeight() * 2 / 3);
			// 设置Drawingcache为true，获得选中项的影像bm，就是后面我们拖动的哪个头像
			itemView.setDrawingCacheEnabled(true);
			Bitmap bm = Bitmap.createBitmap(itemView.getDrawingCache());

			// 准备拖动影像(把影像加入到当前窗口，并没有拖动，拖动操作我们放在onTouchEvent()的move中执行)
			startDrag(bm, y);
		}
		return false;
	}
	return super.onInterceptTouchEvent(ev);
}
```