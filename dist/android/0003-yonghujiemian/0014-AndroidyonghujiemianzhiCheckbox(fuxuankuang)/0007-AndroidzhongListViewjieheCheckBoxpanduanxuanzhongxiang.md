本文主要实现在自定义的ListView布局中加入CheckBox控件，通过判断用户是否选中CheckBox来对ListView的选中项进行相应的操作。通过一个Demo来展示该功能，选中ListView中的某一项，然后点击Button按钮来显示选中了哪些项。
[1] 程序结构图如下：
![img](http://emanual.github.io/md-android/img/view_checkbox/08_checkbox.png) 
其中Person.java是实体类，MainActivity.java是Activity组件类。listitem.xml是自定义的列表每项布局文件。
[2] listitem.xml布局文件源码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:descendantFocusability="blocksDescendants"
        android:orientation="horizontal" >
        <CheckBox
            android:id="@+id/list.select"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
        <TextView
            android:id="@+id/list.name"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_marginLeft="10dp"
            android:layout_weight="1"
            android:text="Name"
            android:textSize="20dp" />
        <TextView
            android:id="@+id/list.address"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            layout_gravity="center"
            android:layout_weight="1"
            android=""
            android:text="Address"
            android:textSize="20dp" />
    </LinearLayout>
</LinearLayout>
```
[3] main.xml布局文件源码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/show"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Show" />
    <ListView
        android:id="@+id/lvperson"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```
[4] Person.java实体类源码如下：
```  
public class Person {
	private String name;
	private String address;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
}
```
[5] MainActivity.java类源码如下：
```  
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import com.andyidea.bean.Person;
import android.app.Activity;
import android.app.AlertDialog;
import android.content.Context;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.ListView;
import android.widget.TextView;
public class MainActivity extends Activity {
	Button show;
	ListView lv;
	List<Person> persons = new ArrayList<Person>();
	Context mContext;
	MyListAdapter adapter;
	List<Integer> listItemID = new ArrayList<Integer>();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mContext = getApplicationContext();
		show = (Button) findViewById(R.id.show);
		lv = (ListView) findViewById(R.id.lvperson);
		initPersonData();
		adapter = new MyListAdapter(persons);
		lv.setAdapter(adapter);
		show.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				listItemID.clear();
				for (int i = 0; i < adapter.mChecked.size(); i++) {
					if (adapter.mChecked.get(i)) {
						listItemID.add(i);
					}
				}
				if (listItemID.size() == 0) {
					AlertDialog.Builder builder1 = new AlertDialog.Builder(
							MainActivity.this);
					builder1.setMessage("没有选中任何记录");
					builder1.show();
				} else {
					StringBuilder sb = new StringBuilder();
					for (int i = 0; i < listItemID.size(); i++) {
						sb.append("ItemID=" + listItemID.get(i) + " . ");
					}
					AlertDialog.Builder builder2 = new AlertDialog.Builder(
							MainActivity.this);
					builder2.setMessage(sb.toString());
					builder2.show();
				}
			}
		});
	}
	 /**
	  * 模拟数据
	  */
	private void initPersonData() {
		Person mPerson;
		for (int i = 1; i <= 12; i++) {
			mPerson = new Person();
			mPerson.setName("Andy" + i);
			mPerson.setAddress("GuangZhou" + i);
			persons.add(mPerson);
		}
	}
	// 自定义ListView适配器
	class MyListAdapter extends BaseAdapter {
		List<Boolean> mChecked;
		HashMap<Integer, View> map = new HashMap<Integer, View>();

		public MyListAdapter(List<Person> list) {
			listPerson = new ArrayList<Person>();
			listPerson = list;
			mChecked = new ArrayList<Boolean>();
			for (int i = 0; i < list.size(); i++) {
				mChecked.add(false);
			}
		}
		@Override
		public int getCount() {
			return listPerson.size();
		}
		@Override
		public Object getItem(int position) {
			return listPerson.get(position);
		}
		@Override
		public long getItemId(int position) {
			return position;
		}
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			View view;
			ViewHolder holder = null;
			if (map.get(position) == null) {
				Log.e("MainActivity", "position1 = " + position);
				LayoutInflater mInflater = (LayoutInflater) mContext
						.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
				view = mInflater.inflate(R.layout.listitem, null);
				holder = new ViewHolder();
				holder.selected = (CheckBox) view
						.findViewById(R.id.list_select);
				holder.name = (TextView) view.findViewById(R.id.list_name);
				holder.address = (TextView) view
						.findViewById(R.id.list_address);
				final int p = position;
				map.put(position, view);
				holder.selected.setOnClickListener(new View.OnClickListener() {
					@Override
					public void onClick(View v) {
						CheckBox cb = (CheckBox) v;
						mChecked.set(p, cb.isChecked());
					}
				});
				view.setTag(holder);
			} else {
				Log.e("MainActivity", "position2 = " + position);
				view = map.get(position);
				holder = (ViewHolder) view.getTag();
			}
			holder.selected.setChecked(mChecked.get(position));
			holder.name.setText(listPerson.get(position).getName());
			holder.address.setText(listPerson.get(position).getAddress());
			return view;
		}
	}
	static class ViewHolder {
		CheckBox selected;
		TextView name;
		TextView address;
	}
}
```
[6] 程序运行后的结果如下：
![img](http://emanual.github.io/md-android/img/view_checkbox/08_checkbox2.jpg) 