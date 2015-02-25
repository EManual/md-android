Gallery是android的一个浏览图片的组件（不只是图片，可以用适配器），Gallery的中文意思也是画廊的意思，就是说把图片排成一行，然后手动滑动阅览。
Gallery的用法很简单，也类似于ListView, GridView。所以如果你对每一个单元图片有更多的信息要显示的话，可以选择适配器。
我做的这个小程序主要目的是将SD卡中已经存在的几张图片显示出来，有两个布局文件很简单，一个是主布局文件，如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Gallery
        android:id="@+id/gallery"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp" />
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_margin="30dp" />
</LinearLayout>
```
此处的ImageView是在Gallery中选中之后放大显示用的，如果不需要，可以不用，第二个布局文件是
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="80dp"
        android:layout_height="100dp" />
    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" />
</LinearLayout>
```
也很简单，就是对每一个单元图片的显示，我在图片的下面显示了一行信息
程序部分也不难，我自己定义了一个适配器，类似于上一篇文章，所以不解释了，直接贴上源码
```  
import java.util.List;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
import android.widget.TextView;
public class ImageAdapter extends BaseAdapter {
	private LayoutInflater inflater = null;
	private Context context = null;
	private List<String> pathList = null;
	public ImageAdapter(Context context, List<String> pathList) {
		this.context = context;
		this.pathList = pathList;
		inflater = LayoutInflater.from(context);
	}
	public int getCount() {
		return pathList != null ? pathList.size() : 0;
	}
	public Object getItem(int arg0) {
		return pathList.get(arg0);
	}
	public long getItemId(int position) {
		return position % pathList.size();
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		if (convertView == null) {
			convertView = inflater.inflate(R.layout.item, null);
		}
		ImageView imageView = (ImageView) convertView.findViewById(R.id.imageView);
		Bitmap bm = BitmapFactory.decodeFile(pathList.get(position).toString());
		imageView.setScaleType(ImageView.ScaleType.FIT_XY);
		imageView.setImageBitmap(bm);
		TextView textView = (TextView) convertView.findViewById(R.id.textView);
		textView.setText("图片" + position);
		return convertView;
	}
}
```
主程序部分根据自己的需要显示的图片做了一个链表，还有在Gallery组件重写了一下其onItemSelected（）方法，用于在下面显示
```  
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Bundle;
import android.os.Environment;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.Gallery;
import android.widget.ImageView;
import android.widget.ViewSwitcher.ViewFactory;
public class GalleryCatalog extends Activity {
	private Gallery gallery = null;
	private ImageView mainImageView = null;
	// 声明图片路径变量
	private List<String> pathList = null;
	private static final String ROOT = Environment
			.getExternalStorageDirectory() + "/tu/";
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		init();
	}
	// 初始化
	private void init() {
		pathList = new ArrayList<String>();
		pathList.add(ROOT + "tu1.jpg");
		pathList.add(ROOT + "tu2.jpg");
		pathList.add(ROOT + "tu3.jpg");
		pathList.add(ROOT + "tu4.jpg");
		pathList.add(ROOT + "tu5.jpg");
		pathList.add(ROOT + "tu6.jpg");
		gallery = (Gallery) findViewById(R.id.gallery);
		mainImageView = (ImageView) findViewById(R.id.imageView);
		mainImageView.setImageResource(R.drawable.icon);
		// 实例化自定义的适配器
		ImageAdapter ia = new ImageAdapter(this, pathList);
		// 加载适配器
		gallery.setAdapter(ia);
		gallery.setOnItemSelectedListener(new GalleryItemSelectedListener());
	}
	private class GalleryItemSelectedListener implements OnItemSelectedListener {
		public void onItemSelected(AdapterView<?> arg0, View arg1, int arg2,
				long arg3) {
			System.out.println(pathList.get(arg2).toString());
			Bitmap bm = BitmapFactory.decodeFile(pathList.get(arg2).toString());
			mainImageView.setImageBitmap(bm);
		}
		public void onNothingSelected(AdapterView<?> arg0) {
		}
	}
}
```