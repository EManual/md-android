大家可能遇到过这样的情况，一个应用要开发手机版和Pad版，在手机中一个ListView就可以搞定，并且是一列显示的，但运行在Pad上时，发现界面太长，需要在Pad上进行多列显示，这时候就希望实现多列的效果，这里我简单实现了个Demo，供大家参考。
1.效果类似Hiapk的安卓市场，如下：
![img](http://emanual.github.io/md-android/img/view_gridview/11_gridview.jpg) 
2.界面布局
main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <GridView
        android:id="@+id/list_gridView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:columnWidth="100dp"
        android:numColumns="5" />
</LinearLayout>
```
Tips:上面的 android:numColumns="5" 表示显示的列数
item_main.xml  在这里布局需要显示的元素
```  
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="10dp"
    android:scrollbars="vertical" >
    <TableRow>
        <TextView
            android:id="@+id/key"
            android:ellipsize="end"
            android:gravity="left" />
    </TableRow>
    <TableRow android:paddingTop="5dp" >
        <TextView
            android:id="@+id/title_1"
            android:ellipsize="end"
            android:gravity="left" />
    </TableRow>
    <TableRow android:paddingTop="5dp" >
        <TextView
            android:id="@+id/title_2"
            android:ellipsize="end"
            android:gravity="left" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true
            android:src="@drawable/item_more" />
    </TableRow>
    <TableRow android:paddingTop="5dp" >
        <TextView
            android:id="@+id/title_3"
            android:ellipsize="end"
            android:gravity="left" />
    </TableRow>
    <TableRow android:paddingTop="5dp" >
        <TextView
            android:id="@+id/title_4"
            android:ellipsize="end"
            android:gravity="left" />
    </TableRow>
</TableLayout>
```
3.Activity处理
其实与使用ListView和GirdView没什么区别
```  
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import android.app.Activity;
import android.os.Bundle;
import android.widget.GridView;
import android.widget.SimpleAdapter;
public class GirdviewActivity extends Activity {
	private GridView gridListView;
	private List<HashMap<String, String>> data;
	private String test = "多列显示Test";
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		findVidwByIds();
		fillData();
		SimpleAdapter sa = new SimpleAdapter(this, data, R.layout.item_main,
				new String[] { "key", "title_1", "title_2", "title_3",
						"title_4" }, new int[] { R.id.key, R.id.title_1,
						R.id.title_2, R.id.title_3, R.id.title_4 });
		gridListView.setAdapter(sa);
	}
	public void findVidwByIds() {
		gridListView = (GridView) findViewById(R.id.list_gridView);
	}
	public void fillData() {
		data = new ArrayList<HashMap<String, String>>();
		for (int i = 0; i < 10; i++) {
			HashMap<String, String> map = new HashMap<String, String>();
			map.put("key", test + i);
			map.put("title_1", test + i);
			map.put("title_2", test + i);
			map.put("title_3", test + i);
			map.put("title_4", test + i);
			data.add(map);
		}
	}
}
```