在android TabHost应用中提到Tab标签中要用ListView填充，这里先实现一个简单的ListView。效果如下图：
![img](P)  
首先肯定是创建android工程，修改res/layout下的main.xml文件如下:
```  
<linearlayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <listview
        android:id="@+id/listview1"
        android:layout_width="fill_parent"
        android:layout_height="match_parent" >
    </listview>
</linearlayout>
```
然后修改Activity中的内容，为ListView赋值：
```  
public class ListViewActivity extends Activity {
	private ListView listView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		listView = (ListView) findViewById(R.id.listview1);
		listView.setAdapter(new ArrayAdapter(this,
				android.R.layout.simple_expandable_list_item_1, getData()));
	}
	private List getData() {
		List data = new ArrayList();
		data.add("测试数据1");
		data.add("测试数据2");
		data.add("测试数据3");
		data.add("测试数据4");
		return data;
	}
}
```
ArrayAdapter包含三个参数，第一个参数表示要填充的布局容器，这里的this表示listView；第二个参数表示列表中每一行的布局，.R.layout.simple_expandable_list_item_1表示每行只能显示文字；第三个参数表示List数据集合。