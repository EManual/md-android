本文提出的优化方法是：在getView()构建一个View列表(List<View>),把最近构建的View存起来，回退时直接从View列表中读取，而不是动态构建。使用这种方法有2个好处：
1.快速读取过去的Item；
2.直接保存View而不是Bitmap,避免了ImageView.setImageBitmaps()带来的延时。
当然坏处就是浪费内存，所以要设定一个上限，超过了就删掉最老的Item。
"CacheAdapter 缓存50个Item"比ViewHolderAdapter的速度略快，"CacheAdapter 缓存75个Item"依然是最快的。
总结："CacheAdapter缓存50个Item"速度与HolderView略快，读取最近的Item速度最快，缓存的Item越多速度越快。"CacheAdapter缓存75个Item"占用内存最少，这是由于一部分图片下载失败，保存的Item的图片为空，实际上是缓存越多Item占用的内存越多。
CacheAdapter.java是实现缓存Item的自定义Adapter,源码如下：
```  
 /**
  * 使用列表缓存过去的Item
  */
public class CacheAdapter extends BaseAdapter {
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
	public CacheAdapter(Context c) {
		mContext = c;
		inflater = (LayoutInflater) mContext
				.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
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
	List<Integer> lstPosition = new ArrayList<Integer>();
	List<View> lstView = new ArrayList<View>();
	List<Integer> lstTimes = new ArrayList<Integer>();
	long startTime = 0;
	public View getView(int position, View convertView, ViewGroup parent) {
		startTime = System.nanoTime();
		if (lstPosition.contains(position) == false) {
			if (lstPosition.size() > 75)// 这里设置缓存的Item数量
			{
				lstPosition.remove(0);// 删除第一项
				lstView.remove(0);// 删除第一项
			}
			convertView = inflater.inflate(R.layout.item, null);
			TextView text = (TextView) convertView.findViewById(R.id.itemText);
			ImageView icon = (ImageView) convertView
					.findViewById(R.id.itemImage);
			text.setText(mItems.get(position).itemTitle);
			new AsyncLoadImage().execute(new Object[] { icon,
					mItems.get(position).itemImageURL });
			lstPosition.add(position);// 添加最新项
			lstView.add(convertView);// 添加最新项
		} else {
			convertView = lstView.get(lstPosition.indexOf(position));
		}
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
	 /**
	  * 异步读取网络图片
	  */
	class AsyncLoadImage extends AsyncTask<Object, Object, Void> {
		@Override
		protected Void doInBackground(Object... params) {
			try {
				ImageView imageView = (ImageView) params[0];
				String url = (String) params[1];
				Bitmap bitmap = getBitmapByUrl(url);
				publishProgress(new Object[] { imageView, bitmap });
			} catch (MalformedURLException e) {
				Log.e("error", e.getMessage());
				e.printStackTrace();
			} catch (IOException e) {
				Log.e("error", e.getMessage());
				e.printStackTrace();
			}
			return null;
		}
		protected void onProgressUpdate(Object... progress) {
			ImageView imageView = (ImageView) progress[0];
			imageView.setImageBitmap((Bitmap) progress[1]);
		}
	}
	static public Bitmap getBitmapByUrl(String urlString)
			throws MalformedURLException, IOException {
		URL url = new URL(urlString);
		URLConnection connection = url.openConnection();
		connection.setConnectTimeout(25000);
		connection.setReadTimeout(90000);
		Bitmap bitmap = BitmapFactory.decodeStream(connection.getInputStream());
		return bitmap;
	}
}
```