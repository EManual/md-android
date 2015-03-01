①　新建项目。
②　定义layout 外部resource 的xml 文件，用来改变layout 的背景
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="Gallery">
        <attr name="android:galleryItemBackground" />
    </declare-styleable>
    <!-- 定义layout 外部resource 的xml 文件，用来改变layout 的背景图。 -->
</resources>
```
③　修改main.xml布局，添加一个Gallery和一个ImageView
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget_absolutelayout"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <Gallery
        android:id="@+id/Gallery_preView"
        android:layout_width="fill_parent"
        android:layout_height="143px"
        android:layout_x="0px"
        android:layout_y="51px" >
    </Gallery>
    <ImageView
        android:id="@+id/ImageView_photo"
        android:layout_width="239px"
        android:layout_height="218px"
        android:layout_x="38px"
        android:layout_y="184px" >
    </ImageView>
</AbsoluteLayout>
```
④　新建一个myImageAdapter类--Gallery的适配器，它继承于BaseAdapter类.
```  
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
public class myImageAdapter extends BaseAdapter {
	@Override
	public int getCount() {
		return 0;
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
		return null;
	}
}
```
⑤　修改mainActivity.java，添加Gallery相关操作
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Gallery;
import android.widget.ImageView;
import android.widget.Toast;
public class Ex_Ctrl_10ME extends Activity {
	/* 定义要使用的对象 */
	private Gallery gallery;
	private ImageView imageview;
	private myImageAdapter imageadapter;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		imageadapter = new myImageAdapter(this);
		/* 通过findViewById 取得 资源对象 */
		gallery = (Gallery) findViewById(R.id.Gallery_preView);
		imageview = (ImageView) findViewById(R.id.ImageView_photo);
		/* 给Gallery设置适配器 把Ex_Ctrl_10ME类传入参数 */
		gallery.setAdapter(imageadapter);
		/* 设置Gallery的点击事件监听器 */
		gallery.setOnItemClickListener(new Gallery.OnItemClickListener() {
			@Override
			public void onItemClick(AdapterView<?> parent, View v,
					int position, long id) {
				/* 显示该图片是几号 */
				Toast.makeText(Ex_Ctrl_10ME.this, "这是图片：" + position + "号",
						Toast.LENGTH_SHORT).show();
				/* 设置大图片 */
				imageview.setBackgroundResource(imageadapter.myImageIds[position]);
			}
		});
	}
}
```
⑥　修改myImageAdapter.java文件，实现相簿浏览效果
```  
import android.content.Context;
import android.content.res.TypedArray;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
public class myImageAdapter extends BaseAdapter {// 自定义的类变量
/* 变量声明 */
	int mGalleryItemBackground;
	private Context context;// 上下文
	/* 构建一Integer array 并取得预加载Drawable 的图片id */
	public Integer[] myImageIds = { R.drawable.photo1, R.drawable.photo2,
			R.drawable.photo3, R.drawable.photo4, R.drawable.photo5,
			R.drawable.photo6, };
	/* 自定义的构造方法 */
	public myImageAdapter(Context context) {
		this.context = context;
		/*
		  * 使用在res/values/attrs.xml 中的<declare-styleable>定义 的Gallery 属性.
		  */
		TypedArray typed_array = context
				.obtainStyledAttributes(R.styleable.Gallery);
		/* 取得Gallery 属性的Index id */
		mGalleryItemBackground = typed_array.getResourceId(
				R.styleable.Gallery_android_galleryItemBackground, 0);
		/* 让对象的styleable 属性能够反复使用 */
		typed_array.recycle();
	}
	/* 重写的方法getCount,返回图片数目 */
	@Override
	public int getCount() {
		return myImageIds.length;
	}
	/* 重写的方法getItemId,返回图像的数组id */
	@Override
	public Object getItem(int position) {
		return position;
	}
	@Override
	public long getItemId(int position) {
		return position;
	}
	/* 重写的方法getView,返回一View 对象 */
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		/* 产生ImageView 对象 */
		ImageView imageview = new ImageView(context);
		/* 设置图片给imageView 对象 */
		imageview.setImageResource(myImageIds[position]);
		/* 重新设置图片的宽高 */
		imageview.setScaleType(ImageView.ScaleType.FIT_XY);
		/* 重新设置Layout 的宽高 */
		imageview.setLayoutParams(new Gallery.LayoutParams(128, 128));
		/* 设置Gallery 背景图 */
		imageview.setBackgroundResource(mGalleryItemBackground);
		/* 返回imageView 对象 */
		return imageview;
	}
}
```
⑦　结果
![img](P)  