网格布局：是一个ViewGroup以网格显示它的子视图（view）元素，即二维的、滚动的网格。网格元素通过ListAdapter自动插入到网格。ListAdapter跟上面的列表布局是一样的，这里就不重复累述了。
下面也通过一个例子来，创建一个显示图片缩略图的网格。当一个元素被选择时，显示该元素在列表中的位置的消息。
1)、首先，将上面实践截取的图片放入res/drawable/
2)、res/layour/main.xml的内容置为如下：这个GridView填满整个屏幕，而且它的属性都很好理解，按英文单词的意思就对了。
```  
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:gravity="center"
    android:horizontalSpacing="10dp"
    android:numColumns="auto_fit"
    android:stretchMode="columnWidth"
    android:verticalSpacing="10dp" />
```
3)、然后，HelloWorld.java文件中onCreate()函数如下：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.main);
	GridView gridview = (GridView) findViewById(R.id.gridview);
	gridview.setAdapter(new ImageAdapter(this));
	gridview.setOnItemClickListener(new OnItemClickListener() {
		public void onItemClick(AdapterView<?> parent, View v,
				int position, long id) {
			Toast.makeText(HelloWorld.this, " " + position,
					Toast.LENGTH_SHORT).show();
		}
	});
}
```
onCreate()函数跟通常一样，首先调用超类的onCreate()函数函数，然后通过setContentView()为活动（Activity）加载布局文件。紧接着是，通过GridView的id获取布局文件中的gridview，然后调用它的setListAdapter(ListAdapter)函数填充它，它的参数是一个我们自定义的ImageAdapter。后面的工作跟列表布局中一样，为监听网格中的元素被点击的事件而做的工作。
4)、实现我们自定义ImageAdapter，新添加一个类文件，它的代码如下：
```  
import android.content.Context;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.GridView;
import android.widget.ImageView;
public class ImageAdapter extends BaseAdapter {
	private Context mContext;
	public ImageAdapter(Context c) {
		mContext = c;
	}
	public int getCount() {
		return mThumbIds.length;
	}
	public Object getItem(int position) {
		return null;
	}
	public long getItemId(int position) {
		return 0;
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		ImageView imageView;
		if (convertView == null) { // if it's not recycled, initialize some
									// attributes
			imageView = new ImageView(mContext);
			imageView.setLayoutParams(new GridView.LayoutParams(85, 85));
			imageView.setScaleType(ImageView.ScaleType.CENTER_CROP);
			imageView.setPadding(8, 8, 8, 8);
		} else {
			imageView = (ImageView) convertView;
		}
		imageView.setImageResource(mThumbIds[position]);
		return imageView;
	}
	// references to our images
	private Integer[] mThumbIds = { R.drawable.linearlayout1,
			R.drawable.linearlayout2, R.drawable.linearlayout3,
			R.drawable.listview, R.drawable.relativelayout,
			R.drawable.tablelayout };
}
```
ImageAdapter类扩展自BaseAdapter，所以首先得实现它所要求必须实现的方法。构造函数和getcount()函数很好理解，而getItem(int)应该返回实际对象在适配器中的特定位置，但是这里我们不需要。类似地，getItemId(int)应该返回元素的行号，但是这里也不需要。
这里重点要介绍的是getView()方法，它为每个要添加到ImageAdapter的图片都创建了一个新的View。当调用这个方法时，一个View是循环再用的，因此要确认对象是否为空。如果是空的话，一个ImageView就被实例化且配置想要的显示属性：
1、setLayoutParams(ViewGroup.LayoutParams)：设置View的高度和宽度，这确保不管drawable中图片的大小，每个图片都被重新设置大小且剪裁以适应这些尺寸。
2、setScaleType(ImageView.ScaleType)：声明图片应该向中心剪裁（如果需要的话）。
3、setPadding(int, int, int, int)：定义补距，如果图片有不同的横纵比，小的补距将导致更多的剪裁以适合设置的ImageView的高度和宽度。
如果View传到getView()不是空的，则本地的ImageView初始化时将循环再用View对象。在getView()方法末尾，position整数传入setImageResource()方法以从mThumbIds数组中选择图片。
运行程序会得到如下结果（点击第一张图片之后）：
![img](http://emanual.github.io/md-android/img/view_gridview/06_gridview.jpg) 