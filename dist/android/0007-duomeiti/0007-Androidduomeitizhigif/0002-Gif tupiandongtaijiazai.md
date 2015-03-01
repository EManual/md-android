Gif 图片动态加载
![img](P)  
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.TextView;
import com.juapk.gif.GifView;
public class TestActivity extends Activity implements OnClickListener {
	private GifView LoadGif;
	private boolean isStop = true;
	private TextView tv;
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.gif);
		LoadGif = (GifView) findViewById(R.id.loadGif);
		LoadGif.setGifImage(R.drawable.loading);
		// LoadGif.setGifImageType(GifImageType.COVER);
		// LoadGif.setShowDimension(300, 300);
		// LoadGif.setGifImage(R.drawable.loading);
		// 操作GIF Loading
		tv = (TextView) findViewById(R.id.stopLoad);
		tv.setOnClickListener(this);
	}
	public void onClick(View v) {
		// 暂停控制
		if (isStop) {
			LoadGif.showCover();
			tv.setText("启动 Loading");
			isStop = false;
		} else {
			LoadGif.showAnimation();
			tv.setText("暂停 Loading");
			isStop = true;
		}
	}
}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="FFFFFF"
    android:orientation="vertical" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="50dip"
        android:layout_gravity="top"
        android:background="99BAF3"
        android:gravity="center"
        android:orientation="vertical" >
        <TextView
            android:id="@+id/tsxt"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="作者:轻描淡写"
            android:textColor="FFFFFF"
            android:textSize="18sp" />
        <TextView
            android:id="@+id/tsxt"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Android交流群:137824028"
            android:textColor="FFFFFF"
            android:textSize="18sp" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center"
        android:orientation="vertical" >
        <com.juapk.gif.GifView
            android:id="@+id/loadGif"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="18dip"
            android:enabled="false"
            android:paddingRight="14px" />
        <TextView
            android:id="@+id/stopLoad"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="18dip"
            android:text="暂停 Loading"
            android:textColor="000000" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:gravity="right"
        android:orientation="vertical" >
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/icon" />
    </LinearLayout>
</LinearLayout>