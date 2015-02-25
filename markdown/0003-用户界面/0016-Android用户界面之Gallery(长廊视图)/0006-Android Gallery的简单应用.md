Step 1:准备图片素材。
将icon2,icon3,icon4,icon5,icon6五张图片导入res/drawable里加上icon.png本身一共有6张图片。
Step 2:新建Android工程,命名为GalleryDemo。
Step 3:设计UI，修改main.xml代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/white"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/myTextView01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_vertical|center_horizontal"
        android:text="@string/hello" />
    <Gallery
        android:id="@+id/myGallery1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="bottom" />
</LinearLayout>
```
Step 4:设计主程序类GalleryDemo.java代码如下: 
```  
import com.android.test.R.drawable;
import android.app.Activity;
import android.content.Context;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
public class GalleryDemo extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		((Gallery) findViewById(R.id.myGallery1)).setAdapter(new ImageAdapter(this));
	}
	public class ImageAdapter extends BaseAdapter {
		/* 类成员 myContext为Context父类 */
		private Context myContext;
		/* 使用res/drawable图片作为图片来源 */
		private int[] myImageIds = { drawable.icon, drawable.icon2,
				drawable.icon3, drawable.icon4, drawable.icon5, drawable.icon6 };
		/* 构造器只有一个参数，即要存储的Context */
		public ImageAdapter(Context c) {
			this.myContext = c;
		}
		/* 返回所有已定义的图片总数量 */
		public int getCount() {
			return this.myImageIds.length;
		}
		/* 利用getItem方法，取得目前容器中图像的数组ID */
		public Object getItem(int position) {
			return position;
		}
		public long getItemId(int position) {
			return position;
		}
		/* 取得目前欲显示的图像View，传入数组ID值使之读取与成像 */
		public View getView(int position, View convertView, ViewGroup parent) {
			/* 创建一个ImageView对象 */
			ImageView i = new ImageView(this.myContext);
			i.setImageResource(this.myImageIds[position]);
			i.setScaleType(ImageView.ScaleType.FIT_XY);
			/* 设置这个ImageView对象的宽高，单位为dip */
			i.setLayoutParams(new Gallery.LayoutParams(120, 120));
			return i;
		}
		/* 依据距离中央的位移量 利用getScale返回views的大小(0.0f to 1.0f) */
		public float getScale(boolean focused, int offset) {
			/* Formula: 1 / (2 ^ offset) */
			return Math.max(0, 1.0f / (float) Math.pow(2, Math.abs(offset)));
		}
	}
}
```