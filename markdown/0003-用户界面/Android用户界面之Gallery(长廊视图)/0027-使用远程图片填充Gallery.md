目的：
了解如何应用非本地图片资源制作Gallery widget。
Layout源代码 (XML)：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Gallery
        id="@+id/gallery"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="bottom" />
</LinearLayout>
```
主程序源代码 (Java)：
```  
import java.io.BufferedInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import java.net.URLConnection;
import android.app.Activity;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
public class GalleryExample extends Activity {
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.main);
		/*
		 [Tags]* Find the gallery defined in the main.xml Apply a new (custom)
		 [Tags]* ImageAdapter to it.
		 [Tags]*/
		((Gallery) findViewById(R.id.gallery))
				.setAdapter(new ImageAdapter(this));
	}
	public class ImageAdapter extends BaseAdapter {
		[Tags]/** The parent context */
		private Context myContext;
		[Tags]/** URL-Strings to some remote images. */
		private String[] myRemoteImages = {
				"http://www.anddev.org/images/tiny_tutheaders/weather_forecast.png",
				"http://www.anddev.org/images/tiny_tutheaders/cellidtogeo.png",
				"http://www.anddev.org/images/tiny_tutheaders/droiddraw.png" };
		[Tags]/** Simple Constructor saving the 'parent' context. */
		public ImageAdapter(Context c) {
			this.myContext = c;
		}
		[Tags]/** Returns the amount of images we have defined. */
		public int getCount() {
			return this.myRemoteImages.length;
		}
		/* Use the array-Positions as unique IDs */
		public Object getItem(int position) {
			return position;
		}
		public long getItemId(int position) {
			return position;
		}
		[Tags]/**
		 [Tags]* Returns a new ImageView to be displayed, depending on the position
		 [Tags]* passed.
		 [Tags]*/
		public View getView(int position, View convertView, ViewGroup parent) {
			ImageView i = new ImageView(this.myContext);
			try {
				/* Open a new URL and get the InputStream to load data from it. */
				URL aURL = new URL(myRemoteImages[position]);
				URLConnection conn = aURL.openConnection();
				conn.connect();
				InputStream is = conn.getInputStream();
				/* Buffered is always good for a performance plus. */
				BufferedInputStream bis = new BufferedInputStream(is);
				/* Decode url-data to a bitmap. */
				Bitmap bm = BitmapFactory.decodeStream(bis);
				bis.close();
				is.close();
				/* Apply the Bitmap to the ImageView that will be returned. */
				i.setImageBitmap(bm);
			} catch (IOException e) {
				i.setImageResource(R.drawable.error);
				Log.e("DEBUGTAG", "Remtoe Image Exception", e);
			}
			/* Image should be scaled as width/height are set. */
			i.setScaleType(ImageView.ScaleType.FIT_CENTER);
			/* Set the Width/Height of the ImageView. */
			i.setLayoutParams(new Gallery.LayoutParams(150, 150));
			return i;
		}
		[Tags]/**
		 [Tags]* Returns the size (0.0f to 1.0f) of the views depending on the
		 [Tags]* 'offset' to the center.
		 [Tags]*/
		public float getScale(boolean focused, int offset) {
			/* Formula: 1 / (2 ^ offset) */
			return Math.max(0, 1.0f / (float) Math.pow(2, Math.abs(offset)));
		}
	}
}
```