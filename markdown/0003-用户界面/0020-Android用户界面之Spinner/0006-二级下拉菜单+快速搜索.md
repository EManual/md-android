#### 一、我们要做什么？
上面有个搜索框，下面是一个二级下拉菜单。
![img](P)  
输入查询内容，下面列表将显示查询结果。
![img](P)  
#### 二、界面设计
（1）这是主框架（部分属性已经省去，请看源码），从上至下分别是文本框，列表，二级列表。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout>
    <LinearLayout android:id="@+id/city_middle" >
        <EditText
            android:id="@+id/txtfind"
            android:hint="请输入" >
        </EditText>
        <ListView android:id="@+id/listfind" >
        </ListView>
        <ExpandableListView android:id="@+id/exList" />
    </LinearLayout>
</LinearLayout>
```
（2）一级菜单栏样式，图片将区别是否展开
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout>
    <TextView android:id="@+id/group" >
    </TextView>
    <ImageView android:id="@+id/tubiao" >
    </ImageView>
</LinearLayout>
```
（3）二级菜单栏样式
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout>
    <TextView android:id="@+id/child" >
    </TextView>
</LinearLayout>
```
#### 三、代码设计
（1） 定义菜单对应数据
```  
public static List<BasicNameValuePair> fatherList = new ArrayList<BasicNameValuePair>();
public static List<List<BasicNameValuePair>> childList = new ArrayList<List<BasicNameValuePair>>();
```
生成测试数据
```  
for (int i = 0; i < 20; i++) {
	fatherList.add(new BasicNameValuePair("father" + i, "father" + i));
	List<BasicNameValuePair> cList = new ArrayList<BasicNameValuePair>();
	for (int j = 0; j < 5; j++) {
		cList.add(new BasicNameValuePair("child" + i + ":" + j, "child
				+ i + ":" + j));
	}
	childList.add(cList);
}
```
（2）定义列表适配器
```  
protected class ListAdapter extends BaseAdapter {
	private LayoutInflater mInflater;
	// 查询结果列表
	private List<BasicNameValuePair> list = new ArrayList<BasicNameValuePair>();
	public ListAdapter(Context context, String strin) {
		mInflater = LayoutInflater.from(context);
		// 查询匹配
		for (int i = 0; i < childList.size(); i++) {
			for (int j = 0; j < childList.get(i).size(); j++) {
				String tmp = childList.get(i).get(j).getValue();
				if (tmp.indexOf(strin) >= 0) {
					list.add(new BasicNameValuePair(childList.get(i).get(j)
							.getName(), tmp));
				}
			}
		}
	}
	public int getCount() {
		return list.size();
	}
	public Object getItem(int position) {
		return position;
	}
	public long getItemId(int position) {
		return position;
	}
	public View getView(final int position, View convertView,
			ViewGroup parent) {
		convertView = mInflater.inflate(R.layout.child, null);
		TextView title = (TextView) convertView.findViewById(R.id.child);
		title.setText(list.get(position).getValue());
		return convertView;
	}
}
```
初始化列表，默认为隐藏
```  
list = (ListView) findViewById(R.id.listfind);
list.setVisibility(View.GONE);
```
（3）定义二级列表适配器
```  
protected class ExAdapter extends BaseExpandableListAdapter {
	@Override
	public int getGroupCount() {
		return fatherList.size();
	}
	@Override
	public int getChildrenCount(int groupPosition) {
		return childList.get(groupPosition).size();
	}
	@Override
	public Object getGroup(int groupPosition) {
		return fatherList.get(groupPosition).getValue();
	}
	@Override
	public Object getChild(int groupPosition, int childPosition) {
		return childList.get(groupPosition).get(childPosition).getValue();
	}
	@Override
	public long getGroupId(int groupPosition) {
		return groupPosition;
	}
	@Override
	public long getChildId(int groupPosition, int childPosition) {
		return childPosition;
	}
	@Override
	public View getGroupView(int groupPosition, boolean isExpanded,
			View convertView, ViewGroup parent) {
		View view = convertView;
		if (view == null) {
			LayoutInflater inflater = (LayoutInflater) getSystemService(Context.LAYOUT_INFLATER_SERVICE);
			view = inflater.inflate(R.layout.group, null);
		}
		TextView t = (TextView) view.findViewById(R.id.group);
		t.setText(fatherList.get(groupPosition).getValue());
		// 展开，改变图片
		ImageView gImg = (ImageView) view.findViewById(R.id.tubiao);
		if (isExpanded)
			gImg.setBackgroundResource(R.drawable.mm_submenu_down_normal);
		else
			gImg.setBackgroundResource(R.drawable.mm_submenu_normal);
		return view;
	}
	@Override
	public View getChildView(int groupPosition, int childPosition,
			boolean isLastChild, View convertView, ViewGroup parent) {
		View view = convertView;
		if (view == null) {
			LayoutInflater inflater = (LayoutInflater) getSystemService(Context.LAYOUT_INFLATER_SERVICE);
			view = inflater.inflate(R.layout.child, null);
		}
		TextView t = (TextView) view.findViewById(R.id.child);
		t.setText(childList.get(groupPosition).get(childPosition).getValue());
		return view;
	}
	@Override
	public boolean hasStableIds() {
		return true;
	}
	@Override
	public boolean isChildSelectable(int groupPosition, int childPosition) {
		return true;
	}
}
```
初始化二级菜单
```  
exList = (ExpandableListView) findViewById(R.id.exList);
exList.setAdapter(new ExAdapter());
exList.setGroupIndicator(null);
exList.setDivider(null);
```
（4）搜索事件，输入改变即触发
```  
txtFind = (EditText) findViewById(R.id.txtfind);
txtFind.addTextChangedListener(new TextWatcher() {
	@Override
	public void beforeTextChanged(CharSequence s, int start, int count,
			int after) {
	}
	@Override
	public void onTextChanged(CharSequence s, int start, int before,
			int count) {
	}
	@Override
	public void afterTextChanged(Editable s) {
		if (s != null && !s.toString().equals("")) {
			list.setAdapter(new ListAdapter(DWinterDemoActivity.this, s
					.toString()));
			list.setVisibility(View.VISIBLE);
			exList.setVisibility(View.GONE);
		} else {
			list.setVisibility(View.GONE);
			exList.setVisibility(View.VISIBLE);
		}
	}
});
```
（5）去除焦点自动弹出输入
```  
getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_STATE_ALWAYS_HIDDEN);
```