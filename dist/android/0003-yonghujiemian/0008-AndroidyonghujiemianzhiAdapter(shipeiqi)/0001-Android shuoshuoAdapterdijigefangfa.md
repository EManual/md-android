Android的Adapter是连接后端数据和前端显示的适配器接口，他有多种抽象类，在使用Gallery时候，我们继承的BaseAdapter就是他的一个子类。
要实现BaseAdapter这个子类，我们要实现它的四个方法，
```  
public int getCount() 
public Object getItem(int position) 
public long getItemId(int position)
public View getView(int position, View convertView, ViewGroup parent) 
```
第一个方法和第四个方法比较好理解,第一个方法是返回我们后台一共有多少数据，最后一个方法是返回一个加载了数据的view，以便于前面控件的调用。那么第二个方法和第三个方法又是做什么用的呢?我们需要返回什么值呢？
察看Google的文档，他在Gallery这个类中实现了一个BaseAdapter的子类，如下所示:
```  
public class ImageAdapter extends BaseAdapter {
	int mGalleryItemBackground;
	private Context mContext;
	private Integer[] mImageIds = { R.drawable.sample_1, R.drawable.sample_2,
			R.drawable.sample_3, R.drawable.sample_4, R.drawable.sample_5,
			R.drawable.sample_6, R.drawable.sample_7 };
	public ImageAdapter(Context c) {
		mContext = c;
		TypedArray a = obtainStyledAttributes(R.styleable.HelloGallery);
		mGalleryItemBackground = a.getResourceId(
				R.styleable.HelloGallery_android_galleryItemBackground, 0);
		a.recycle();
	}
	public int getCount() {
		return mImageIds.length;
	}
	public Object getItem(int position) {
		return position;
	}
	public long getItemId(int position) {
		return position;
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		ImageView i = new ImageView(mContext);
		i.setImageResource(mImageIds[position]);
		i.setLayoutParams(new Gallery.LayoutParams(150, 100));
		i.setScaleType(ImageView.ScaleType.FIT_XY);
		i.setBackgroundResource(mGalleryItemBackground);
		return i;
	}
}
```
里面的getItem和getItemId这两个方法统统地返回position这个变量，很奇怪的两个方法，尤其是getItem，不是要返回Object吗?怎么返回了一个数字了?虽然数字也是一个Object，但是很明显这个方法看起来不是要返回一个数字的。
那么我们就来看源代码吧,BaseAdapter继承了SpinnerAdapter，我们在SpinnerAdapter中去找这个方法的调用，没找到。BaseAdapter还继承了ListAdapter，我们去找，还是没有找到。
既然都没有找到，那么这两个方法只能是适用这个Adapter的View来调用的，察看Gallery这个类，发现他继承了AbsSpinner，而AbsSpinner继承了AdapterView，在AdapterView类中，我们找到了如下的两个方法，
```  
public class Snippet {
	public Object getItemAtPosition(int position) {
		T adapter = getAdapter();
		return (adapter == null || position < 0) ? null : adapter
				.getItem(position);
	}
	public long getItemIdAtPosition(int position) {
		T adapter = getAdapter();
		return (adapter == null || position < 0) ? INVALID_ROW_ID : adapter
				.getItemId(position);
	}
}
```
这两个方法是public的方法,说明我们可以直接调用Gallery的这两个方法的,那么AdapterView中有没有调用这两个方法呢?
经过察看源代码发现, public Object getItemAtPosition(int position)这个方法单纯是为我们调用的,android在源码中没有调用这方法
public long getItemIdAtPosition(int position)这个方法会设置mNextSelectedRowId这个变量，而这个变量的设置好以后android也并没有去取用,只提供了一个公共方法
public long getSelectedItemId() { return mNextSelectedRowId; }来返回这个变量。