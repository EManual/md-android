其中if(lstPosition.size()>75)是设置缓存的Item数量的关键地方,这里缓存75个Item。
ViewHolderAdapter.java是实现ViewHolder加载Item的自定义Adapter,源码如下：
```  
[Tags]/**
 [Tags]* 使用ViewHolder加载Item
 [Tags]*/
public class ViewHolderAdapter extends BaseAdapter {
	public class Item {
		public String itemImageURL;
		public String itemTitle;
		public Item(String itemImageURL, String itemTitle) {
			this.itemImageURL = itemImageURL;
			this.itemTitle = itemTitle;
		}
	}
	private Context mContext;
	private ArrayList<Item> mItems = new ArrayList<Item>();
	LayoutInflater inflater;
	public ViewHolderAdapter(Context c) {
		mContext = c;
		inflater = (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
	}
	public void addItem(String itemImageURL, String itemTitle) {
		mItems.add(new Item(itemImageURL, itemTitle));
	}
	public int getCount() {
		return mItems.size();
	}
	public Item getItem(int position) {
		return mItems.get(position);
	}
	public long getItemId(int position) {
		return position;
	}
	static class ViewHolder {
		TextView text;
		ImageView icon;
	}
	List<Integer> lstTimes = new ArrayList<Integer>();
	long startTime = 0;
	public View getView(int position, View convertView, ViewGroup parent) {
		startTime = System.nanoTime();
		ViewHolder holder;
		if (convertView == null) {
			convertView = inflater.inflate(R.layout.item, null);
			holder = new ViewHolder();
			holder.text = (TextView) convertView.findViewById(R.id.itemText);
			holder.icon = (ImageView) convertView.findViewById(R.id.itemImage);
			convertView.setTag(holder);
		} else {
			holder = (ViewHolder) convertView.getTag();
		}
		holder.text.setText(mItems.get(position).itemTitle);
		new AsyncLoadImage().execute(new Object[] { holder.icon,
				mItems.get(position).itemImageURL });
		int endTime = (int) (System.nanoTime() - startTime);
		lstTimes.add(endTime);
		if (lstTimes.size() == 10) {
			int total = 0;
			for (int i = 0; i < lstTimes.size(); i++)
				total = total + lstTimes.get(i);
			Log.e("10个所花的时间：" + total / 1000 + " μs", "所用内存：
					+ Runtime.getRuntime().totalMemory() / 1024 + " KB");
			lstTimes.clear();
		}
		return convertView;
	}
	[Tags]/**
	 [Tags]* 异步读取网络图片
	 [Tags]*/
	class AsyncLoadImage extends AsyncTask<Object, Object, Void> {
		@Override
		protected Void doInBackground(Object... params) {
			try {
				ImageView imageView = (ImageView) params[0];
				String url = (String) params[1];
				Bitmap bitmap = CacheAdapter.getBitmapByUrl(url);
				publishProgress(new Object[] { imageView, bitmap });
			} catch (MalformedURLException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
			return null;
		}
		protected void onProgressUpdate(Object... progress) {
			ImageView imageView = (ImageView) progress[0];
			imageView.setImageBitmap((Bitmap) progress[1]);
		}
	}
}
```
testPerformance.java是主程序，通过注释符就可以分别测试CacheAdapter与ViewHolderAdapter的性能，源码如下：
```  
public class testPerformance extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		this.setTitle("android平板上的GridView视图缓存优化-----hellogv");
		GridView gridview = (GridView) findViewById(R.id.gridview);
		CacheAdapter adapter = new CacheAdapter(this);
		// ViewHolderAdapter adapter=new ViewHolderAdapter(this);
		gridview.setAdapter(adapter);
		String urlImage = "";// 请自己选择网络上的静态图片
		for (int i = 0; i < 100; i++) {
			adapter.addItem(urlImage, "第" + i + "项");
		}
	}
}
```