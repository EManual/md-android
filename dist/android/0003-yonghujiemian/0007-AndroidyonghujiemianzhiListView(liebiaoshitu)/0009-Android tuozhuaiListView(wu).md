三、拓展
10.分组拖拽拓展。
前面我们一直在数据源中添加了分组标签A组，B组的，下面我们就把数据分成A组，B组。
1）定义分组标签样式布局drag_list_item_tag.xml。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:background="555555"
    android:padding="5dip"
    android:paddingLeft="10dip" >
    <!-- 文本框的ID保持不变 -->
    <TextView
        android:id="@+id/drag_list_item_text"
        android:layout_width="wrap_content"
        android:layout_height="20dip"
        android:gravity="center_vertical"
        android:textColor="ffffff" />
    <!-- 去除来右边拖拽图像，分组标签是不能随意拖动的 -->
</LinearLayout>
```
2）修改DragListAdapter中getView()方法。
```  
@Override
public View getView(int position, View convertView, ViewGroup parent) {
	View view = convertView;
	if (groupKey.contains(getItem(position))) {
		// 如果是分组标签，就加载分组标签的布局文件，两个布局文件显示效果不同
		view = LayoutInflater.from(getContext()).inflate(
				R.layout.drag_list_item_tag, null);
	} else {
		// 如果是正常数据项标签，就加在正常数据项的布局文件
		view = LayoutInflater.from(getContext()).inflate(
				R.layout.drag_list_item, null);
	}
	TextView textView = (TextView) view.findViewById(R.id.drag_list_item_text);
	textView.setText(getItem(position));
	return view;
}
```
3）禁用分组标签项的响应事件，在DragListAapter中重写方法isEnable()。
刚好因为在分组标签中去掉了拖拽图像，所以点击在分组标签中的话，dragImageView为空，不会有被拖动的效果了，这就是前面说的顺手的一个妙用了。
```  
@Override
public boolean isEnabled(int position) {
	if (groupKey.contains(getItem(position))) {
		// 如果是分组标签，返回false，不能选中，不能点击
		return false;
	}
	return super.isEnabled(position);
}
```
4）标签项是不能拖动位置的，所以我们要修改一下onDrop()中的上边界控制。
```  
// 上边界改为1
if (y < getChildAt(1).getTop()) {
	// 超出上边界
	dragPosition = 1;
} else if (y > getChildAt(getChildCount() - 1).getBottom()) {
	// 超出下边界
	dragPosition = getAdapter().getCount() - 1;
}
```