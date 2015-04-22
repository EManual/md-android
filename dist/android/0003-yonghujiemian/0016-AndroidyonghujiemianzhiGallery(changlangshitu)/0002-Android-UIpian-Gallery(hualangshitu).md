玩转Android---UI篇---Gallery（画廊视图）
Gallery能够水平显示其内容，一般用来浏览图片，被选中的选项位于中间，并且可以相应事件显示信息。下面结合ImageSwitcher组件来实现一个通过缩略图来浏览图片的程序，具体步骤如下
#### 第一步：
创建一个Andorid工程"GalleryTest”，该工程的入口是Activity类GalleryTest继承Activity并实现OnItemSelectedListener和ViewFactory接口，来实现图片和视图的创建
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.ViewSwitcher.ViewFactory;
//继承Activity，实现onItemSelectedListener和ViewFactory接口
public class GalleryTest extends Activity implements OnItemSelectedListener,
		ViewFactory {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
	@Override
	public View makeView() {
		return null;
	}
	@Override
	public void onItemSelected(AdapterView<?> arg0, View arg1, int arg2,
			long arg3) {
	}
	@Override
	public void onNothingSelected(AdapterView<?> arg0) {
	}
}
```
#### 第二步：
在工程的res\drawable\目录下添加7张图片和对应的缩略图
#### 第三步：
在工程res\layout\目录下创建一个布局文件main.xml，在其中那个添加一个Gallery组件和一个ImageSwitcher组件，并设置相应的属性
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ImageSwitcher
        android:id="@+id/switcher"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_alignParentLeft="true
        android:layout_alignParentTop="true" />
    <Gallery
        android:id="@+id/gallery"
        android:layout_width="fill_parent"
        android:layout_height="60dp"
        android:layout_alignParentBottom="true
        android:layout_alignParentLeft="true
        android:background="55000000"
        android:gravity="center_vertical"
        android:spacing="16dp" />
</LinearLayout>
```
#### 第四步：
在GalleryTest顶部声明使用到的ImageSwitcher实例图片资源Integer数组
```  
public class GalleryTest extends Activity implements OnItemSelectedListener,
		ViewFactory {
	 /** Called when the activity is first created. */
	// 声明ImageSwitcher
	private ImageSwitcher switcher;
	// 缩略图片id数组
	private Integer[] thumbids = { R.drawable.thumb0, R.drawable.thumb1,
			R.drawable.thumb2, R.drawable.thumb3, R.drawable.thumb4,
			R.drawable.thumb5, R.drawable.thumb6, R.drawable.thumb7 };
	// 图片id数组
	private Integer[] imgids = { R.drawable.img0, R.drawable.img1,
			R.drawable.img2, R.drawable.img3, R.drawable.img4, R.drawable.img5,
			R.drawable.img6, R.drawable.img7 };
}
```
#### 第五步：
在GalleryTest的onCreate()方法中，将窗口样式设置为无标题，设置当前布局视图，获得ImageSwitcher实例，并设置渐进渐出动画，获得Gallery实例
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	// 设置窗口特征无标题
	requestWindowFeature(Window.FEATURE_NO_TITLE);
	setContentView(R.layout.main);
	// 通过findViewById方法获得ImageSwitcher对象
	switcher = (ImageSwitcher) findViewById(R.id.switcher);
	// 为ImageSwitcher设置工厂
	switcher.setFactory(this);
	// 设置动画渐入效果
	switcher.setInAnimation(AnimationUtils.loadAnimation(this,
			android.R.anim.fade_in));
	// 设置动画渐出效果
	switcher.setOutAnimation(AnimationUtils.loadAnimation(this,
			android.R.anim.fade_out));
	// 通过findViewById方法获得Gallery对象
	Gallery g = (Gallery) findViewById(R.id.gallery);
}
```
#### 第六步：
创建内部类ImageAdapter，该类继承BaseAdapter，为Gallery设置Adapter实例
```  
public class ImageAdapter extends BaseAdapter {
	// 构造方法
	public ImageAdapter(Context c) {
		mContext = c;
	}
	// 获得数量
	public int getCount() {
		return thumbids.length;
	}
	// 获得当前选项
	public Object getItem(int position) {
		return position;
	}
	// 获得当前选项ID
	public long getItemId(int position) {
		return position;
	}
	// 获得View对象
	public View getView(int position, View convertView, ViewGroup parent) {
		// 实例化ImageView对象
		ImageView i = new ImageView(mContext);
		// 设置缩略图片资源
		i.setImageResource(thumbids[position]);
		// 设置边界对齐
		i.setAdjustViewBounds(true);
		// 设置布局参数
		i.setLayoutParams(new Gallery.LayoutParams(LayoutParams.WRAP_CONTENT,
				LayoutParams.WRAP_CONTENT));
		// 设置背景资源
		i.setBackgroundResource(R.drawable.picturefrom);
		return i;
	}
	private Context mContext;
}
```
#### 第七步：
实现onItemSelected()方法，更换图片
```  
@Override
public void onItemSelected(AdapterView<?> adapter, View v, int position, long id) {
	switcher.setImageResource(imgids[position]);
}
```
#### 第八步：
实现makeView()方法，为ImageView设置布局格式
```  
@Override
public View makeView() {
	// 创建ImageView
	ImageView i = new ImageView(this);
	// 设置背景颜色
	i.setBackgroundColor(0xFF000000);
	// 设置精度类型
	i.setScaleType(ImageView.ScaleType.FIT_CENTER);
	// 设置布局参数
	i.setLayoutParams(new ImageSwitcher.LayoutParams(
			LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT));
	return i;
}
```
#### 第九步：
为Gallery添加Adapter并添加OnItemSelectedListener监听器
```  
g.setAdapter(new ImageAdapter(this));
g.setOnItemSelectedListener(this);
```
至此，全部，结束，运行结果如下
![img](http://emanual.github.io/md-android/img/view_gallery/03_gallery.jpg) 
完整源代码：
```  
import android.app.Activity;
import android.content.Context;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup;
import android.view.Window;
import android.view.animation.AnimationUtils;
import android.widget.AdapterView;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageSwitcher;
import android.widget.ImageView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.Gallery.LayoutParams;
import android.widget.ViewSwitcher.ViewFactory;
public class GalleryTest extends Activity implements OnItemSelectedListener,
		ViewFactory {
	private ImageSwitcher mSwitcher;
	private Integer[] mThumbIds = { R.drawable.thumb0, R.drawable.thumb1,
			R.drawable.thumb2, R.drawable.thumb3, R.drawable.thumb4,
			R.drawable.thumb5, R.drawable.thumb6, R.drawable.thumb7 };
	private Integer[] mImageIds = { R.drawable.img0, R.drawable.img1,
			R.drawable.img2, R.drawable.img3, R.drawable.img4, R.drawable.img5,
			R.drawable.img6, R.drawable.img7 };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.main);
		mSwitcher = (ImageSwitcher) findViewById(R.id.switcher);
		mSwitcher.setFactory(this);
		mSwitcher.setInAnimation(AnimationUtils.loadAnimation(this,
				android.R.anim.fade_in));
		mSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this,
				android.R.anim.fade_out));
		Gallery g = (Gallery) findViewById(R.id.gallery);
		g.setAdapter(new ImageAdapter(this));
		g.setOnItemSelectedListener(this);
	}
	public class ImageAdapter extends BaseAdapter {
		public ImageAdapter(Context c) {
			mContext = c;
		}
		public int getCount() {
			return mThumbIds.length;
		}
		public Object getItem(int position) {
			return position;
		}
		public long getItemId(int position) {
			return position;
		}
		public View getView(int position, View convertView, ViewGroup parent) {
			ImageView i = new ImageView(mContext);
			i.setImageResource(mThumbIds[position]);
			i.setAdjustViewBounds(true);
			i.setLayoutParams(new Gallery.LayoutParams(
					LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT));
			i.setBackgroundResource(R.drawable.picturefrom);
			return i;
		}
		private Context mContext;
	}
	@Override
	public void onItemSelected(AdapterView<?> adapter, View v, int position,
			long id) {
		mSwitcher.setImageResource(mImageIds[position]);
	}
	@Override
	public void onNothingSelected(AdapterView<?> arg0) {

	}
	@Override
	public View makeView() {
		ImageView i = new ImageView(this);
		i.setBackgroundColor(0xFF000000);
		i.setScaleType(ImageView.ScaleType.FIT_CENTER);
		i.setLayoutParams(new ImageSwitcher.LayoutParams(
				LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT));
		return i;
	}
}
```
XML:
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <ImageSwitcher
        android:id="@+id/switcher"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_alignParentLeft="true
        android:layout_alignParentTop="true" />
    <Gallery
        android:id="@+id/gallery"
        android:layout_width="fill_parent"
        android:layout_height="60dp"
        android:layout_alignParentBottom="true
        android:layout_alignParentLeft="true
        android:background="55000000"
        android:gravity="center_vertical"
        android:spacing="16dp" />
</RelativeLayout>
```