ListView的使用2
①ListView在XML文件中声明
```  
<ListView android:id="@+id/list"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	/>
```
②ListView在java文件中获得
```  
listview = (ListView) findViewById(R.id.list)；
//从list.xml中获得ListView对象list
```
③声明和定义、初始化一个ArrayList
```  
private ArrayList<Map<String, Object>> arrayList;
arrayList= new ArrayList<Map<String, Object>>();
Map<String, Object> map;
map= new HashMap<String, Object>();
map.put("System", "Linux");
map.put("Ability", "Cool");
arrayList.add(item);
```
④声明和定义、初始化一个SimpleAdepter
```  
SimpleAdapter adapter;
adapter= new SimpleAdapter(this, coll,
android.R.layout.simple_list_item_1,
new String[] { "System" }, new int[] { android.R.id.text1 });
```
⑤LixtView设置适配器
```  
listview.setAdapter(adapter);//设置适配器
```
⑥为ListView设置选项事件监听器
```  
listview.setOnItemClickListener(listener);
OnItemClickListener listener = new OnItemClickListener() {
	public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
		textview.setTextColor(Color.YELLOW);
		textview.setText(coll.get(arg2).get("prod_type").toString());
	}
};//事件处理接口实现
```