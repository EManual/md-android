先看效果图：
![img](P)  
Java源代码如下：
```  
import android.app.Activity;
import android.content.Context;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.GridView;
import android.widget.ImageView;
import android.widget.TextView;
public class MenuPanelActivity extends Activity {
	private GridView gridView;
	private int[] mImageIds = { R.drawable.calculator, R.drawable.camera,
			R.drawable.compass, R.drawable.ebook, R.drawable.email,
			R.drawable.games, R.drawable.map, R.drawable.message,
			R.drawable.multimedia, R.drawable.music, R.drawable.phone,
			R.drawable.radio, R.drawable.video, };
	private int[] TitleTexts = { R.string.calculator, R.string.camera,
			R.string.compass, R.string.ebook, R.string.email, R.string.games,
			R.string.map, R.string.message, R.string.multimedia,
			R.string.music, R.string.phone, R.string.radio, R.string.video, };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		gridView = (GridView) this.findViewById(R.id.GridViewId);
		gridView.setAdapter(new gridViewAdapter(mImageIds, TitleTexts));
	}
	public class gridViewAdapter extends BaseAdapter {
		private View[] itemViews;
		public gridViewAdapter(int[] mImageIds, int[] TitleTexts) {
			itemViews = new View[mImageIds.length];
			for (int i = 0; i < itemViews.length; i++) {
				itemViews = makeItemView(mImageIds, TitleTexts);
			}
		}
		public int getCount() {
			return itemViews.length;
		}
		public View getItem(int position) {
			return itemViews[position];
		}
		public long getItemId(int position) {
			return position;
		}
		private View makeItemView(int strmImageIds, int strTitleTexts) {
			LayoutInflater inflater = (LayoutInflater) MenuPanelActivity.this
					.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
			View itemView = inflater.inflate(R.layout.menuitem, null);
			TextView title = (TextView) itemView.findViewById(R.id.TextItemId);
			title.setText(strTitleTexts);
			ImageView image = (ImageView) itemView
					.findViewById(R.id.ImageItemId);
			image.setImageResource(strmImageIds);
			image.setScaleType(ImageView.ScaleType.FIT_CENTER);
			return itemView;
		}
		public View getView(int position, View convertView, ViewGroup parent) {
			if (convertView == null)
				return itemViews[position];
			return convertView;
		}
	}
}
```
main.xml代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/GridViewId"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:columnWidth="90dip"
    android:gravity="center"
    android:horizontalSpacing="10dip"
    android:numColumns="auto_fit"
    android:paddingRight="5dip"
    android:stretchMode="columnWidth"
    android:verticalSpacing="10dip" />
```
menuitem.xml代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" >
    <ImageView
        android:id="@+id/ImageItemId"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true" >
    </ImageView>
    <TextView
        android:id="@+id/TextItemId"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/ImageItemId"
        android:layout_centerHorizontal="true
        android:text="TextView" >
    </TextView>
</RelativeLayout>
```
strings.xml代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">MenuPanel</string>
    <string name="calculator">calculator</string>
    <string name="camera">camera</string>
    <string name="compass">compass</string>
    <string name="ebook">ebook</string>
    <string name="email">email</string>
    <string name="games">games</string>
    <string name="map">map</string>
    <string name="message">message</string>
    <string name="multimedia">multimedia</string>
    <string name="music">music</string>
    <string name="phone">phone</string>
    <string name="radio">radio</string>
    <string name="video">video</string>
</resources>
```