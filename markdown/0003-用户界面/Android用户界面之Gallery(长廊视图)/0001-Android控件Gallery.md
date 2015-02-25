1、布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <Gallery
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/gallery"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" >
        <Gallery
            android:id="@+id/gallery1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="center_vertical"
            android:spacing="16dp" />
    </LinearLayout>
</LinearLayout>
```
2、属性文件
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="TogglePrefAttrs" >
        <attr name="android:preferenceLayoutChild" />
    </declare-styleable>
    <!-- These are the attributes that we want to retrieve from the themein view/Gallery1.java -->
    <declare-styleable name="Gallery1" >
        <attr name="android:galleryItemBackground" />
    </declare-styleable>
    <declare-styleable name="LabelView" >
        <attr
            name="text"
            format="string" />
        <attr
            name="textColor"
            format="color" />
        <attr
            name="textSize"
            format="dimension" />
    </declare-styleable>
</resources>
```
3、主要代码：
```  
import android.R.layout;
import android.app.Activity;
import android.content.Context;
import android.content.res.TypedArray;
import android.database.Cursor;
import android.os.Bundle;
import android.view.ContextMenu;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.view.ContextMenu.ContextMenuInfo;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
import android.widget.SimpleCursorAdapter;
import android.widget.SpinnerAdapter;
import android.widget.Toast;
import android.widget.AdapterView.AdapterContextMenuInfo;
public class GalleryDemo extends Activity {
	private Gallery gallery;
	private Gallery gallery1;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.gallerypage);
		gallery = (Gallery) findViewById(R.id.gallery);
		gallery.setAdapter(new ImageAdapter(this));
		registerForContextMenu(gallery);
		Cursor c = getContentResolver().query(People.CONTENT_URI, null, null,
				null, null);
		startManagingCursor(c);
		SpinnerAdapter adapter = new SimpleCursorAdapter(this,
				android.R.layout.simple_gallery_item, c,
				new String[] { People.NAME }, new int[] { android.R.id.text1 });
		gallery1 = (Gallery) findViewById(R.id.gallery1);
		gallery1.setAdapter(adapter);
	}
	@Override
	public void onCreateContextMenu(ContextMenu menu, View v,
			ContextMenuInfo menuInfo)
	{
		menu.add("Action");
	}
	@Override
	public boolean onContextItemSelected(MenuItem item) {
		AdapterContextMenuInfo info = (AdapterContextMenuInfo) item.getMenuInfo();
		Toast.makeText(this, "Longpress: " + info.position, Toast.LENGTH_SHORT).show();
		return true;
	}
	public class ImageAdapter extends BaseAdapter {
		int mGalleryItemBackground;
		private Context mContext;
		private Integer[] mImageIds = { R.drawable.b, R.drawable.c,
				R.drawable.d, R.drawable.f, R.drawable.g };
		public ImageAdapter(Context context) {
			mContext = context;
			TypedArray a = obtainStyledAttributes(R.styleable.Gallery1);
			mGalleryItemBackground = a.getResourceId(
					R.styleable.Gallery1_android_galleryItemBackground, 0);
			a.recycle();
		}
		@Override
		public int getCount() {
			return mImageIds.length;
		}
		@Override
		public Object getItem(int position) {
			return position;
		}
		@Override
		public long getItemId(int position) {
			return position;
		}
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			ImageView i = new ImageView(mContext);
			i.setImageResource(mImageIds[position]);
			i.setScaleType(ImageView.ScaleType.FIT_XY);
			i.setLayoutParams(new Gallery.LayoutParams(300, 400));
			// The preferred Gallery item background
			i.setBackgroundResource(mGalleryItemBackground);
			return i;
		}
	}
}
```
效果图：
![img](P)  