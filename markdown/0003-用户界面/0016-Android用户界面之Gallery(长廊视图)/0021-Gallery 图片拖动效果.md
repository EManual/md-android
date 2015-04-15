Gallery效果
![img](http://emanual.github.io/md-android/img/view_gallery/22_gallery.jpg) 
废话不多说了，上代码。效果就是上面这样的
首先定义一个存放资源的容器，ImageAdapter继承于BaseAdapter
```  
import android.content.Context;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
public class ImageAdapter extends BaseAdapter {
	private Context mContext;
	private int[] imgResource = { R.drawable.img1, R.drawable.img2,
			R.drawable.img3, R.drawable.img4, R.drawable.img5, R.drawable.img6 };
	public ImageAdapter(Context c) {
		mContext = c;
	}
	@Override
	public int getCount() {
		return imgResource.length;
	}
	@Override
	public Object getItem(int arg0) {
		return null;
	}
	@Override
	public long getItemId(int position) {
		return 0;
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		ImageView imageView = new ImageView(mContext);
		imageView.setImageResource(imgResource[position]);// 为ImageView设置要显示的内容
		imageView.setLayoutParams(new Gallery.LayoutParams(500, 500));// 设置布局图片以120X120显示
		imageView.setScaleType(ImageView.ScaleType.FIT_CENTER);
		return imageView;
	}
}
```
在Xml中定义一个Gallery
```  
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding = "10px" >
</Gallery>
```
在Activity类中调用
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.Gallery;
public class Gallery01 extends Activity {
	private Gallery gallery;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		gallery = (Gallery) findViewById(R.id.gallery);
		gallery.setAdapter(new ImageAdapter(Gallery01.this));
	}
}
```
点击事件和ListView一样，都是OnItem