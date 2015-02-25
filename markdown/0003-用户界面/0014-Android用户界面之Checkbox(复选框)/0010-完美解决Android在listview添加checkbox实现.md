刚刚看了朋友发了一篇"完美解决Android在listview添加checkbox实现批量操作问题"，觉得问题本身根本不复杂。废话不多话，接下来就说明问题吧！
由于只是为了说明问题，所以界面啥的就不讲究了，先上一张图吧！
![img](P)  
主界面CheckBoxinListViewActivity.java代码如下：
```  
public class CheckBoxinListViewActivity extends Activity {
	private MyAdapter adapter;
	private ListView listview;
	private Button checkAll;
	private Button noCheckAll;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		listview = (ListView) findViewById(R.id.listview);
		checkAll = (Button) findViewById(R.id.button1);
		noCheckAll = (Button) findViewById(R.id.button2);
		adapter = new MyAdapter();
		listview.setAdapter(adapter);
		checkAll.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				adapter.checkAll();
			}
		});
		noCheckAll.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				adapter.noCheckAll();
			}
		});
	}
	private class MyAdapter extends BaseAdapter {
		private ArrayList<Message> list = new ArrayList<Message>();
		public MyAdapter() {
			for (int i = 1; i <= 100; i++) {
				list.add(new Message("item_" + i));
			}
		}
		public void checkAll() {
			for (Message msg : list) {
				msg.isCheck = true;
			}
			notifyDataSetChanged();
		}
		public void noCheckAll() {
			for (Message msg : list) {
				msg.isCheck = false;
			}
			notifyDataSetChanged();
		}
		@Override
		public int getCount() {
			return list.size();
		}
		@Override
		public Object getItem(int position) {
			return null;
		}
		@Override
		public long getItemId(int position) {
			return 0;
		}
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			ViewHolder viewHolder;
			if (convertView == null) {
				LayoutInflater inflater = LayoutInflater
						.from(CheckBoxinListViewActivity.this);
				convertView = inflater.inflate(R.layout.listview_item, null);
				viewHolder = new ViewHolder();
				viewHolder.checkBox = (CheckBox) convertView
						.findViewById(R.id.checkBox1);
				convertView.setTag(viewHolder);
			} else {
				viewHolder = (ViewHolder) convertView.getTag();
			}
			final Message msg = list.get(position);
			viewHolder.checkBox.setText(msg.str);
			viewHolder.checkBox.setChecked(msg.isCheck);
			// 注意这里设置的不是onCheckedChangListener，还是值得思考一下的
			viewHolder.checkBox.setOnClickListener(new OnClickListener() {
				@Override
				public void onClick(View v) {
					if (msg.isCheck) {
						msg.isCheck = false;
					} else {
						msg.isCheck = true;
					}

				}
			});
			return convertView;
		}
	}
	private class ViewHolder {
		CheckBox checkBox;
	}
}
```
适配器所适配的消息Message.java如下：
```  
public class Message {
	public boolean isCheck;
	public String str;
	public Message(String str) {
		this.str = str;
	}
}
```