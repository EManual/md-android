费话不多说，直接看代码：
values/attrs.xml: 
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<declare-styleable name="Gallery">
		<attr name="android:galleryItemBackground" />
	</declare-styleable>
</resources>
```
layout/img.xml: 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Gallery
        android:id="@+id/galley"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_x="12px"
        android:layout_y="106px" />
    <ImageView
        android:id="@+id/image"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
ImageAdapter.java: 
```  
import java.util.List;
import org.mdx.R;
import android.content.Context;
import android.content.res.TypedArray;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
public class ImageAdapter extends BaseAdapter {
	int mGalleryItemBacjground;
	private Context mContext;
	private List<Integer> list;
	public ImageAdapter(Context c, List<Integer> li) {
		mContext = c;
		list = li;
		TypedArray a = c.obtainStyledAttributes(R.styleable.Gallery);
		mGalleryItemBacjground = a.getResourceId(
				R.styleable.Gallery_android_galleryItemBackground, 0);
		a.recycle();
	}
	public int getCount() {
		return list.size();
	}
	public Object getItem(int position) {
		return list.get(position % list.size()).intValue();
	}
	public long getItemId(int arg0) {
		return arg0;
	}
	public View getView(int position, View arg1, ViewGroup arg2) {
		ImageView i = new ImageView(mContext);
		Bitmap bm = BitmapFactory.decodeResource(mContext.getResources(), list
				.get(position % list.size()).intValue());
		i.setImageBitmap(bm);
		i.setScaleType(ImageView.ScaleType.FIT_XY);
		i.setLayoutParams(new Gallery.LayoutParams(136, 88));
		i.setBackgroundResource(mGalleryItemBacjground);
		return i;
	}
}
```
ImgActivity.java: 
```  
import java.util.ArrayList;
import java.util.List;
import org.mdx.R;
import org.mdx.core.activity.adapter.ImageAdapter;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.Gallery;
import android.widget.ImageView;
public class ImgActivity extends Activity {
	private ImageView imageView;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.img);
		imageView = (ImageView) findViewById(R.id.image);
		final Gallery g = (Gallery) findViewById(R.id.galley);
		final ImageAdapter imageAdapter = new ImageAdapter(this, getImages());
		g.setAdapter(imageAdapter);
		g.setOnItemClickListener(new OnItemClickListener() {
			public void onItemClick(AdapterView<?> parent, View view,
					int position, long id) {
				imageView.setImageResource(((Integer) imageAdapter
						.getItem(position)).intValue());
			}
		});
		g.setOnItemSelectedListener(new OnItemSelectedListener() {
			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				imageView.setImageResource(((Integer) imageAdapter
						.getItem(position)).intValue());
			}
			public void onNothingSelected(AdapterView<?> parent) {
				imageView.setImageResource(((Integer) imageAdapter.getItem(g
						.getSelectedItemPosition())).intValue());
			}
		});
	}
	private List<Integer> getImages() {
		List<Integer> list = new ArrayList<Integer>();
		list.add(new Integer(R.drawable.a));
		list.add(new Integer(R.drawable.b));
		list.add(new Integer(R.drawable.c));
		list.add(new Integer(R.drawable.d));
		list.add(new Integer(R.drawable.e));
		list.add(new Integer(R.drawable.f));
		list.add(new Integer(R.drawable.g));
		return list;
	}
}
```