本例展示的是GridView的实现，其中图片资源来自《Android核心技术与实例详解》一书，代码部分参照官方文档完成。效果如下
![img](P)  
代码如下：
1、string.xml文件
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">Sample5_Components</string>
    <string name="hello">您选择了%s</string>
    <string name="andy">Andy Rubin \nAndroid的创造者</string>
    <string name="bill">Bill Joy \nJava创造者之一</string>
    <string name="edgar">Edgar F. Codd \n关系数据库之父</string>
    <string name="torvalds">Linus Torvalds \nLinux之父</string>
    <string name="turing">Turing Alan \nIT的祖师爷</string>
    <string name="andy_t">Andy Rubin</string>
    <string name="bill_t">Bill Joy</string>
    <string name="edgar_t">Edgar F. Codd</string>
    <string name="torvalds_t">Linus Torvalds父</string>
    <string name="turing_t">Turing Alan</string>
</resources>
```
2、布局文件：gridview.xml和gridview_child.xml，分别表示GridView和每一个单元格的布局。
（1）gridview.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dip"  //单元格宽度
    android:numColumns="auto_fit"  //自动调整每行显示的单元格数量
    android:horizontalSpacing="5dip"
    android:verticalSpacing="5dip"
    android:stretchMode="columnWidth"
    android:gravity="center">
</GridView>
```
（2）gridview_child.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/linear"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="8dip" >
    <ImageView
        android:id="@+id/gridimg"
        android:layout_width="100dip"
        android:layout_height="98dip" />
    <TextView
        android:id="@+id/gridtext"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:maxLines="1" />
</LinearLayout>
```
3、Activity文件
```  
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.GridView;
import android.widget.SimpleAdapter;
import android.widget.Toast;
public class GridViewDemo extends Activity {
	static final int ICON_COUNT = 5;
	private String img = "img";
	private String text = "text";
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.gridview);
		SimpleAdapter sAdapter = new SimpleAdapter(this, getData(),
				R.layout.gridview_child, new String[] { text, img }, new int[] {
						R.id.gridtext, R.id.gridimg });
		GridView gView = (GridView) findViewById(R.id.gridview);
		gView.setAdapter(sAdapter);
		gView.setOnItemClickListener(new OnItemClickListener() {
			@Override
			public void onItemClick(AdapterView<?> parent, View view,
					int position, long id) {
				Toast.makeText(
						GridViewDemo.this,
						String.format(getString(R.string.hello),
								getString(R.string.andy + position)),
						Toast.LENGTH_SHORT).show();
			}
		});
	}
	private List<Map<String, Object>> getData() {
		List<Map<String, Object>> list = new ArrayList<Map<String, Object>>();
		Map<String, Object> map = null;
		for (int i = 0; i < ICON_COUNT; i++) {
			map = new HashMap<String, Object>();
			map.put(text, getString(R.string.andy_t + i));
			map.put(img, R.drawable.aandy + i);
			list.add(map);
		}
		return list;
	}
}
```
至此，一个简单的小例子就完成了。另外，也可以使用其他的Adapter，比如，我们可以写一个Adapter类，使其继承BaseAdapter类，重写getView方法即可，针对此例该方法稍显麻烦，所以没有选择。getData方法返回的类型为List<Map<String,Object>>，每一个List的item对应一个GridView的item，而且布局文件R.layout.gridview_child对应item的布局。