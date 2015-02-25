一、准备。
1.需求问题
初步：实现列表的拖拽效果
拓展：实现分组列表的拖拽效果。
下面以初步实现为例子，逐步展开实现步骤。
2.搭建主界面DragListActivity.java和主布局drag_list_activity.xml。
```  
public class DragListActivity extends Activity {
	// 数据列表
	private List<String> list = null;
	// 数据适配器
	private DragListAdapter adapter = null;
	// 存放分组标签
	public static List<String> groupKey = new ArrayList<String>();
	// 分组一
	private List<String> navList = new ArrayList<String>();
	// 分组二
	private List<String> moreList = new ArrayList<String>();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.drag_list_activity);
		// 初始化样本数据
		initData();
		// 后面会介绍DragListView
		DragListView dragListView = (DragListView) findViewById(R.id.drag_list);
		adapter = new DragListAdapter(this, list);
		dragListView.setAdapter(adapter);
	}
}
```
3.列表项的布局drag_list_item.xml。 
```  
<?xml version="1.0" encoding="utf-8"?>
<!-- 强调一点，使用相对布局 -->
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" >
    <TextView
        android:id="@+id/drag_list_item_text"
        android:layout_width="wrap_content"
        android:layout_height="@dimen/drag_item_normal_height"
        android:layout_alignParentLeft="true"
        android:layout_centerVertical="true"
        android:gravity="center_vertical"
        android:paddingLeft="5dip" />
    <ImageView
        android:id="@+id/drag_list_item_image"
        android:layout_width="wrap_content"
        android:layout_height="@dimen/drag_item_normal_height"
        android:layout_alignParentRight="true"
        android:layout_centerVertical="true"
        android:src="@drawable/list_icon" />
</RelativeLayout>
```
4.准备样本数据。
我已经准备好了两组数据，在前面提到的initData()方法中执行初始化。
```  
public void initData() {
	// 数据结果
	list = new ArrayList<String>();
	// groupKey存放的是分组标签
	groupKey.add("A组");
	groupKey.add("B组");
	for (int i = 0; i < 5; i++) {
		navList.add("A选项" + i);
	}
	list.add("A组");
	list.addAll(navList);
	for (int i = 0; i < 8; i++) {
		moreList.add("B选项" + i);
	}
	list.add("B组");
	list.addAll(moreList);
}
```