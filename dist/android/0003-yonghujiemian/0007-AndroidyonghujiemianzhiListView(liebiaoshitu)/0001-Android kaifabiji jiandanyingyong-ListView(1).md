ListView的使用（1）
①ListView的声明、定义
```  
ListView list=new ListView(this);
```
②数组适配器的声明定义
```  
String []name=new String[]{"Java","C++","C","C","VB","XML",".NET","J"};
ArrayAdapter<String> arrayadapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1,name);
```
③给ListView设置数组适配器
```  
list.setAdapter(arrayadapter);
```
④设置ListView的元素被选中时的事件处理监听器
```  
list.setOnItemClickListener(this);
```
⑤事件处理监听器方法
```  
@Override
public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
	setTitle(GetData(arg2));
}
```
⑥让ListView中的内容变化，重新设置一个ArrayAdapter即可
```  
String []score=new String[]{"Android","BlackBerry","J2ME","Symbian","Windows Mobile","OpenMOKO","Broncho","IPhone"};
ArrayAdapter<String> arratadapter2=new ArrayAdapter<String>(this, android.R.layout.simple_expandable_list_item_1,score);
list.setAdapter(arratadapter2);
```