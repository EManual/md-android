以下通过TabHost实现android选项卡。
main.xml布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:id="@+id/tab01"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:gravity="center_horizontal"
        android:orientation="vertical" >
        <ImageView
            android:id="@+id/iv01"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:scaleType="fitXY"
            android:src="@drawable/andy" />
        <TextView
            android:id="@+id/tv01"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Android的创造者: Andy Rubin"
            android:textSize="24dip" />
    </LinearLayout>
    <LinearLayout
        android:id="@+id/tab02"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:gravity="center_horizontal"
        android:orientation="vertical" >
        <ImageView
            android:id="@+id/iv02"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:scaleType="fitXY"
            android:src="@drawable/bill" />
        <TextView
            android:id="@+id/tv02"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Java创造者之一: Bill Joy"
            android:textSize="24dip" />
    </LinearLayout>
    <LinearLayout
        android:id="@+id/tab03"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:gravity="center_horizontal"
        android:orientation="vertical" >
        <ImageView
            android:id="@+id/iv03"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:scaleType="fitXY"
            android:src="@drawable/torvalds" />
        <TextView
            android:id="@+id/tv03"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Linux之父: Linus Torvalds"
            android:textSize="24dip" />
    </LinearLayout>
</LinearLayout>
```
TabHostActivity类
```  
import android.app.TabActivity;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.widget.TabHost;
public class TabHostActivity extends TabActivity {
	private TabHost tab = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		tab = this.getTabHost();
		LayoutInflater.from(this).inflate(R.layout.main,
				tab.getTabContentView(), true);
		tab.addTab(tab
				.newTabSpec("选项卡一")
				.setIndicator("选项卡一",
						getResources().getDrawable(R.drawable.png1))
				.setContent(R.id.tab01));
		tab.addTab(tab
				.newTabSpec("选项卡二")
				.setIndicator("选项卡二",
						getResources().getDrawable(R.drawable.png2))
				.setContent(R.id.tab02));
		tab.addTab(tab
				.newTabSpec("选项卡三")
				.setIndicator("选项卡三",
						getResources().getDrawable(R.drawable.png3))
				.setContent(R.id.tab03));
	}
}
```
![img](P)  