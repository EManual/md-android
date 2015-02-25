首先是创建三个全局变量：
```  
SimpleAdapter listItemAdapter;  // ListView的适配器 
ArrayList<HashMap<String, Object>> listItem;  // ListView的数据源，这里是一个HashMap的列表            
ListView myList;  // ListView控件
```
然后在Activity的onCreate函数中对变量进行初始化：
```  
listItem = new ArrayList<HashMap<String, Object>>();
listItemAdapter = new SimpleAdapter(this, listItem, R.layout.mylayout,
new String[]{"image", "title", "text"},
new int[]{R.id.ItemImage, R.id.ItemTitle, R.id.ItemText});
myList = (ListView)findViewById(R.id.TaxiList);
myList.setAdapter(listItemAdapter);
```
添加两个私有的功能函数：
```  
private void addItem()
{
	HashMap<String, Object> map = new HashMap<String, Object>();
	map.put("image", R.drawable.icon);
	map.put("title", "标题");
	map.put("text", "要显示的内容");
	listItem.add(map);
	listItemAdapter.notifyDataSetChanged();
}
private void deleteItem()
{
	int size = listItem.size();
	if (size > 0)
	{
		listItem.remove(listItem.size() - 1);
		listItemAdapter.notifyDataSetChanged();
	}
}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/RelativeLayout01"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:paddingBottom="4dip"
    android:paddingLeft="12dip"
    android:paddingRight="12dip" >
    <ImageView
        android:id="@+id/ItemImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingTop="4dip"
        android:src="@drawable/taxi1" >
    </ImageView>
    <TextView
        android:id="@+id/ItemTitle"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@+id/ItemImage"
        android:text="DaZhong Taxi Corporation"
        android:textSize="24dip" >
    </TextView>
    <TextView
        android:id="@+id/ItemText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/ItemTitle"
        android:layout_toRightOf="@+id/ItemImage"
        android:text="Tel:021-67786874" >
    </TextView>
</RelativeLayout>
```