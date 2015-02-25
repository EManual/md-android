说之前谈下我对Adapter的看法:坛子上经常见很多帖子问如何如何使用什么什么view什么view的,在Android中,我个人经验是理解概念,然后翻看sdk和源代码和金山词霸,一般绝大部分的问题都可以解决滴
Android是完全遵循MVC模式设计的框架,Activity是Controller,layout是View
因为layout五花八门,很多数据都不能直接绑定上去,所以Android引入了Adapter这个机制作为复杂数据的展示的转换载体,所以各种Adapter只不过是转换的方式和能力不一样而已,没什么大不了的
不多说,今天来看下几种常用的Adapter:
```  
ArrayAdapter
SimpleAdapter
SimpleCursorAdapter
SimpleExpandableListAdapter
SimpleCursorTreeAdapter
```
例子就不贴了,API DEMO里大把是,自己copy去,我就说下我的理解和他们的区别
```  
ArrayAdapter:顾名思义,专门负责将数组结构的数据适配进view中的,最简单,常用于demo和Spinner中
SimpleAdapter:从名字上看不出什么所以然,其他这个东西很给力,在正常情况下他的灵活性最好,扩展性也最强(ViewBinder)
SimpleCursorAdapter:拥有上者的扩展性和灵活性,同时可以将Cursor进行适配
SimpleExpandableListAdapter:这玩意就漏也了,只能适配到TextView上,在简单的UI中可以为ExpandableListView提供数据
SimpleCursorTreeAdapter:这是Adapter的终极Boss...-_-|||... ViewBinder+Cursor+Expandable...三位一体
```
下面我用来做例子的是Spinner,这个Spinner和APIDemo里面的Spinner不一样,List中的每一个项是绑定了一个Value的
没错,这就是一个HTML 中的 select+option...
1)先来贴一段APIDemo里面的标准Spinner:
```  
//1 使用传统方法的ArrayAdapter进行适
ArrayAdapter<CharSequence> adapter1 = ArrayAdapter.createFromResource(
				this,
				R.array.simple_array,//被适配的资源
				android.R.layout.simple_spinner_item//根据资源使用什么layout来渲染
);
//设置展开后使用什么layout来渲染
adapter1.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
```
这里使用了两个系统中的layout,你不妨进去看看他们是什么,我这就不贴了,
simple_spinner_item是个TextView,simple_spinner_dropdown_item是个CheckedTextView,那么问题就来了,他们和Spinner有什么联系???
这里推敲一下就能得到Spinner的实现原理
1) Spinner其实是个组合视图,由三个部分组成(未打开时的TextView加个Spinner的倒三角背景/点击时弹出来的AlertDialog+ListView/ListView中的每一项是个CheckedText),明白了这个,你完全可以自己实现一个...如果不觉得烦的话
2) 明白了上面的分析后就应该知道上述的ApiDemo里为什么是那样做的了,所以,看看SpinnerAdapter有几个实现:
Known Indirect Subclasses
ArrayAdapter<T>, BaseAdapter, CursorAdapter, ResourceCursorAdapter, SimpleAdapter, SimpleCursorAdapter
也就是说除了ArrayAdapter以外,你还可以使用另外5种Adapter来适配并填充数据,根据我们上面的分析,这里也可以使用SimplerAdapter+ViewBinder来实现我们的需求,就是有点麻烦而已:
```  
//2 也可以使用SimpleAdapter+ViewBinder进行渲染 
SimpleAdapter adapter2 = new SimpleAdapter(
				this,
				data,
				android.R.layout.simple_spinner_item,
				new String[]{"data"},
				new int[]{android.R.id.text1});
adapter2.setViewBinder(new ViewBinder() {
		
		@Override
		public boolean setViewValue(View view, Object data, String textRepresentation) {
				switch (view.getId()) {
						case android.R.id.text1:
								//这里你想怎么绑都得
								TextView tv = (TextView) view;
								Map<String, String> map = (Map<String, String>) data;
								
								tv.setText(map.get("key"));
								tv.setTag(map.get("value"));
								
								return true;
				}
				
				return false;
		}
});
adapter2.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
```
ViewBinder就是一我们混在Adapter里面的间谍,他只在关键时刻出场(渲染视图并绑定值时),用这个东西可以实现任何形式的绑定
3)好,就有人说,每次都那么绑不累的啊,ok,那我们再封装多一点,我们来修改一下ArrayAdapter,改成可以绑定Tag的ArrayAdapter:
```  
public class BindableSpinnerAdapter<T> extends BaseAdapter implements Filterable {
	public static final String SEPARATOR = "=>";
	//...
}
```
把ArrayAdapter的东东贴进去,我们修改createViewFromResource这个关键方法:
```  
T item = getItem(position);
//这里处理一下,在绑定Text的时候顺道把Tag一起绑定上去
String s = item.toString();
String key = "";
String value = "";
if (!TextUtils.isEmpty(s) && TextUtils.indexOf(s, SEPARATOR) != -1) {
		key = s.substring(0, s.indexOf(SEPARATOR));

		value = s.substring(s.indexOf(SEPARATOR) + SEPARATOR.length());

		text.setTag(value);
		text.setText(key);
} else {
		if (item instanceof CharSequence) {
				text.setText((CharSequence) item);
		} else {
				text.setText(item.toString());
		}
}
return view;
```
由于目前的res下面只有array没有map,我又懒得自己解析xml了,于是变通一下,仍然使用string-array
```  
<string-array name="simple_map">
	<item>key1 => value1</item>
	<item>key2 => value2</item>
	<item>key3 => value3</item>
	<item>key4 => value4</item>
	<item>key5 => value5</item>
</string-array>
```
在Activity中我们就可以一步到位了:
```  
BindableSpinnerAdapter<CharSequence> adapter3 = new BindableSpinnerAdapter<CharSequence>(
		Main.this,
		android.R.layout.simple_spinner_item,
		getResources().getTextArray(R.array.simple_map)
);
adapter3.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
```
Adapter很强大,很好用
最后来个在2.3带的Galaxy Tab上面跑的图O(∩_∩)O哈哈~,来个代码
![img](P)  