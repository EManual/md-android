我们经常遇见这样的事情，在listview的item中包含有textview和checkBox。我们既想获取listitem的点击事件，又想获取listitem中textview的点击事件和listitem中checkBox的点击事件，那么有没有办法实现呢？答案是肯定的，我们只需重新创建listview的适配器继承BaseAdpter就可以了。另外如果有checkBox或者imageview在内的话就必须设置它聚焦为false。
关键点：
1.listview的适配器要继承BaseAdpt
2.checkBox或者imageview在内的话就必须设置它聚焦为false。
```  
public class Main extends Activity {
	private ListView list;
	private ListAdapter listadapter;
	private String[] arr;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		arr = new String[] { "111", "222", "333" };
		// 绑定Layout里面的ListView
		list = (ListView) findViewById(R.id.ListView);
		listadapter = new ListAdapter();
		// 添加并且显示
		list.setAdapter(listadapter);
		// 添加点击事件
		list.setOnItemClickListener(new OnItemClickListener() {
			public void onItemClick(AdapterView<?> parent, View view,
					int position, long id) {
				// 这里放Item点击事件
				Toast.makeText(Main.this, "Item点击事件", Toast.LENGTH_SHORT).show();
			}
		});
	}
	private class ListAdapter extends BaseAdapter {
		public int getCount() {
			return arr.length;
		}
		public Object getItem(int position) {
			return position;
		}
		public long getItemId(int position) {
			return position;
		}
		public View getView(int position, View view, ViewGroup parent) {
			// 获取布局文件
			if (view == null) {
				view = getLayoutInflater().inflate(R.layout.listview, null);
			}
			// 获取控件
			TextView name = (TextView) view.findViewById(R.id.wishname);
			CheckBox ck = (CheckBox) view.findViewById(R.id.checkBox1);
			if (arr != null) {
				name.setText(arr[position]);
				name.setOnClickListener(new OnClickListener() {
					@Override
					public void onClick(View v) {
						// 这里放点击改变事件
						Toast.makeText(Main.this, "TextView点击事件", Toast.LENGTH_SHORT).show();
					}
				});
				ck.setOnCheckedChangeListener(new OnCheckedChangeListener() {
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						// 这里放点击改变事件
						Toast.makeText(Main.this, "CheckBox点击事件", Toast.LENGTH_SHORT).show();
					}
				});
			}
			return view;
		}
	}
}
```
主页面的xml布局代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ListView
        android:id="@+id/ListView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:padding="5dip" >
    </ListView>
</LinearLayout>
```
listitem的xml布局文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/linear"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:padding="10dip" >
    <TextView
        android:id="@+id/wishname"
        android:layout_width="100px"
        android:layout_height="wrap_content"
        android:gravity="left"
        android:text="TextView01"
        android:textSize="20dip" />
    <CheckBox
        android:id="@+id/checkBox1"
        android:layout_width="40px"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true
        android:layout_marginLeft="140dp"
        android:focusable="false" >
    </CheckBox>
</LinearLayout>
```