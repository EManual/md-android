ListActivity的使用
①定义一个类 继承自ListActivity
```  
public class ListActivityOfMe extends ListActivity {}
```
②声明和定义、初始化一个ArrayList
```  
private ArrayList<Map<String, Object>> arrayList;
arrayList= new ArrayList<Map<String, Object>>();
Map<String, Object> map;
map = new HashMap<String, Object>();
map.put("System", "Linux");
map.put("Ability", "Cool");
arrayList.add(item);
```
③声明和定义、初始化一个SimpleAdepter
```  
SimpleAdapter adapter;
adapter = new SimpleAdapter(this, arrayList,
android.R.layout.simple_list_item_1,
new String[] { "System" }, new int[] { android.R.id.text1 });
```
④LixtView设置适配器
```  
this.setAdapter(adapter);//设置适配器
```
⑤选项按键事件处理，不需要.setOnItemClickListener();
直接实现以下方法
```  
protected void onListItemClick(ListView l, View v, int position, long id) {
	super.onListItemClick(l, v, position, id);
	actionTo(position);
}
```