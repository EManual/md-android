How can I create a list where when you reach the end of the list I am notified so I can load more items?
Thanks,One solution is to implement an OnScrollListener and make changes (like adding items, etc.) to the ListAdapter at a convenient state in its onScrollStateChanged method.
The following ListActivity shows a list of integers, starting with 40, adding 10 per scroll-stop.
```  
public class Test extends ListActivity implements OnScrollListener {
	Aleph0 adapter = new Aleph0();
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setListAdapter(adapter);
		getListView().setOnScrollListener(this);
	}
	public void onScroll(AbsListView v, int i, int j, int k) {
	}
	public void onScrollStateChanged(AbsListView v, int state) {
		if (state == OnScrollListener.SCROLL_STATE_IDLE) {
			adapter.count += 10;
			adapter.notifyDataSetChanged();
		}
	}
	class Aleph0 extends BaseAdapter {
		int count = 40;
		public int getCount() {
			return count;
		}
		public Object getItem(int pos) {
			return pos;
		}
		public long getItemId(int pos) {
			return pos;
		}
		public View getView(int pos, View v, ViewGroup p) {
			TextView view = new TextView(Test.this);
			view.setText("entry " + pos);
			return view;
		}
	}
}
```
You should obviously use separate threads for long running actions(like loading web-data) and might want to indicate progress in the lastlist item (like the market or gmail apps do).
循环list,英文看不懂没关系，可以把代码试试就明白。
http://stackoverflow.com/questions/1080811/android-endless-list
以 上原文出处，有兴趣的可以注册一下，有问题时，可以在里面发问。挺好的一个国外国站