GridView宫格视图实践
①　新建工程。
②　在res/drawable目录下添加名称为a.png---p.png的图片。
③　修改main.xml布局，添加一个GridView、一个ImageView。
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget0"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <GridView
        android:id="@+id/grid"
        android:layout_width="fill_parent"
        android:layout_height="210px"
        android:columnWidth="52px"
        android:numColumns="5"
        android:padding="30dip" >
        <!-- GridView设置为五列 边距为30pid -->
    </GridView>
    <ImageView
        android:id="@+id/ImageView_Big"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="95px"
        android:layout_y="250px" >
    </ImageView>
</AbsoluteLayout>
```
④　GridView的定义与实例化
```  
/*定义类对象*/
private GridView my_gridview;
/*从xml中获取UI资源对象*/
my_gridview = (GridView) findViewById(R.id.grid);
```
⑤　GridView的图像内容设置与ImageAdapter
```  
/*新建一个自定义的ImageAdapter*/
myImageViewAdapter = new ImageAdapter(this);
/*为GridView对象设置一个ImageAdapter*/
my_gridview.setAdapter(myImageViewAdapter);
```
⑥　内部类 ImageAdapter，实现了BaseAdapter
```  
private class myImageAdapter extends BaseAdapter {
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
⑦　GridView的图片Items点击事件处理
```  
/*为GridView添加图片Items点击事件监听器*/
my_gridview.setOnItemClickListener(this);
@Override
public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
	/*点击GridView中图片Items事件处理*/
}
```
⑧　GridView移动后选中图片Items的事件处理
```  
/*为GridView添加图片Items移动选中事件监听器*/
my_gridview.setOnItemSelectedListener(this);
@Override
public void onItemSelected(AdapterView<?> arg0, View arg1, int arg2,long arg3) {
	/*GridView中的图片移动焦点选中时事件处理*/
}
/*未选中GridView中的图片Items事件处理*/
@Override
public void onNothingSelected(AdapterView<?> arg0) {
	// TODO Auto-generated method stub
}
```
⑨　修改mainActivity.java来实现图片点击和图片移动选中的效果
```  
/*导入要使用的包*/
import android.app.Activity;
import android.app.AlertDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.content.DialogInterface.OnClickListener;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.BaseAdapter;
import android.widget.GridView;
import android.widget.ImageView;
public class GridViewTest extends Activity implements
		GridView.OnItemClickListener, GridView.OnItemSelectedListener {
	/* 定义类对象 */
	private GridView my_gridview;
	private ImageView big_imageView;
	private ImageAdapter myImageViewAdapter;
	/* 内部类，实现一个图片适配器 */
	public class ImageAdapter extends BaseAdapter {
		/* myContext为上下文 */
		private Context myContext;
		/* GridView用来加载图片的ImageView */
		private ImageView the_imageView;
		// 这是图片资源ID的数组
		private Integer[] mImageIds = { R.drawable.a, R.drawable.b,
				R.drawable.c, R.drawable.d, R.drawable.e, R.drawable.f,
				R.drawable.g, R.drawable.h, R.drawable.i, R.drawable.j,
				R.drawable.k, R.drawable.l, R.drawable.m, R.drawable.n,
				R.drawable.o, R.drawable.p };
		/* 构造方法 */
		public ImageAdapter(Context myContext) {
			this.myContext = myContext;
			/* 传入一个Context，本例中传入的是GridViewTest */
		}
		/* 返回资源ID数组长度 */
		@Override
		public int getCount() {
			return mImageIds.length;
		}
		/* 得到Item */
		@Override
		public Object getItem(int position) {
			return position;
		}
		/* 获取Items的ID */
		@Override
		public long getItemId(int position) {
			return position;
		}
		/* 获取要显示的View对象 */
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			/* 创建一个ImageView */
			the_imageView = new ImageView(myContext);
			// 设置图像源于资源ID。
			the_imageView.setImageResource(mImageIds[position]);
			/* 使ImageView与边界适应 */
			the_imageView.setAdjustViewBounds(true);
			/* 设置背景图片的风格 */
			the_imageView.setBackgroundResource(android.R.drawable.picture_frame);
			/* 返回带有多个图片ID的ImageView */
			return the_imageView;
		}
		/* 自定义获取对应位置的图片ID */
		public Integer getcheckedImageIDPostion(int theindex) {
			return mImageIds[theindex];
		}
	}
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		/* 设置主屏布局 */
		setContentView(R.layout.main);
		/* 从xml中获取UI资源对象 */
		my_gridview = (GridView) findViewById(R.id.grid);
		big_imageView = (ImageView) findViewById(R.id.ImageView_Big);
		/* 新建一个自定义的ImageAdapter */
		myImageViewAdapter = new ImageAdapter(this);
		/* 为GridView对象设置一个ImageAdapter */
		my_gridview.setAdapter(myImageViewAdapter);
		/* 为GridView添加图片Items点击事件监听器 */
		my_gridview.setOnItemClickListener(this);
		/* 为GridView添加图片Items移动选中事件监听器 */
		my_gridview.setOnItemSelectedListener(this);
	}
	@Override
	public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
		/* 点击GridView中图片Items后显示一个AlterDialog提示框 */
		new AlertDialog.Builder(this).setTitle("图片浏览")
		/* 获得对应的图片并显示 */
		.setIcon(myImageViewAdapter.getcheckedImageIDPostion(arg2))
		/* 添加一个按钮 */
		.setPositiveButton("返回", new OnClickListener() {
			@Override
			public void onClick(DialogInterface dialog, int which) {
			}
			/* 显示提示框 */
		}).show();
	}
	@Override
	public void onItemSelected(AdapterView<?> arg0, View arg1, int arg2,
			long arg3) {
		/*
		  * GridView中的图片移动焦点选中时，下面的大图ImageView显示相应的大图片
		  */
		big_imageView.setImageResource(myImageViewAdapter
				.getcheckedImageIDPostion(arg2));
	}
	/* 未选中GridView中的图片Items事件处理 */
	@Override
	public void onNothingSelected(AdapterView<?> arg0) {
	}
}
```
⑩　结果
![img](P)  