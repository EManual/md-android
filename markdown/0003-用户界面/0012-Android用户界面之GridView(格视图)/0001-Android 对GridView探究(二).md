代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="eoe.gv"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".GvActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".TestActivity1"
            android:label="@string/test_name1" />
        <activity
            android:name=".TestActivity2"
            android:label="@string/test_name2" />
        <activity
            android:name=".TestActivity3"
            android:label="@string/test_name3" />
    </application>
    <uses-sdk android:minSdkVersion="7" />
</manifest>
```
跳转类TestActivity1、TestActivity2、TestActivity3
```  
import android.app.Activity;
import android.os.Bundle;
public class TestActivity1 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
	}
}
import android.app.Activity;
import android.os.Bundle;
public class TestActivity2 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
	}
}
import android.app.Activity;
import android.os.Bundle;
public class TestActivity3 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
	}
}
```
类GvActivity
```  
import java.util.ArrayList;
import java.util.HashMap;
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.GridView;
import android.widget.SimpleAdapter;
import android.widget.Toast;
import android.widget.AdapterView.OnItemClickListener;
public class GvActivity extends Activity {
	private String texts[] = null;
	private int images[] = null;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		images = new int[] { R.drawable.p1, R.drawable.p2, R.drawable.p3,
				R.drawable.p4, R.drawable.p5, R.drawable.p6, R.drawable.p7,
				R.drawable.p8 };
		texts = new String[] { "宫式布局1", "宫式布局2", "宫式布局3", "宫式布局4", "宫式布局5",
				"宫式布局6", "宫式布局7", "宫式布局8" };
		GridView gridview = (GridView) findViewById(R.id.gridview);
		ArrayList<HashMap<String, Object>> lstImageItem = new ArrayList<HashMap<String, Object>>();
		for (int i = 0; i < 8; i++) {
			HashMap<String, Object> map = new HashMap<String, Object>();
			map.put("itemImage", images[i]);
			map.put("itemText", texts[i]);
			lstImageItem.add(map);
		}
		SimpleAdapter saImageItems = new SimpleAdapter(this, lstImageItem,// 数据源
				R.layout.night_item,// 显示布局
				new String[] { "itemImage", "itemText" }, new int[] {
						R.id.itemImage, R.id.itemText });
		gridview.setAdapter(saImageItems);
		gridview.setOnItemClickListener(new ItemClickListener());
	}
	class ItemClickListener implements OnItemClickListener {
		 /**
		  * 点击项时触发事件
		  * @param parent
		  *            发生点击动作的AdapterView
		  * @param view
		  *            在AdapterView中被点击的视图(它是由adapter提供的一个视图)。
		  * @param position
		  *            视图在adapter中的位置。
		  * @param rowid
		  *            被点击元素的行id。
		  */
		public void onItemClick(AdapterView<?> parent, View view, int position,
				long rowid) {
			HashMap<String, Object> item = (HashMap<String, Object>) parent
					.getItemAtPosition(position);
			// 获取数据源的属性值
			String itemText = (String) item.get("itemText");
			Object object = item.get("itemImage");
			Toast.makeText(GvActivity.this, itemText, Toast.LENGTH_LONG).show();
			// 根据图片进行相应的跳转
			switch (images[position]) {
			case R.drawable.p1:
				startActivity(new Intent(GvActivity.this, TestActivity1.class));// 启动另一个Activity
				finish();// 结束此Activity，可回收
				break;
			case R.drawable.p2:
				startActivity(new Intent(GvActivity.this, TestActivity2.class));
				finish();
				break;
			case R.drawable.p3:
				startActivity(new Intent(GvActivity.this, TestActivity3.class));
				finish();
				break;
			}
		}
	}
}
```