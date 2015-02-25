#### 1、JSON(JavaScript Object Notation) 定义：
一种轻量级的数据交换格式，具有良好的可读和便于快速编写的特性。业内主流技术为其提供了完整的解决方案（有点类似于正则表达式，获得了当今大部分语言的支持），从而可以在不同平台间进行数据交换。JSON采用兼容性很高的文本格式，同时也具备类似于C语言体系的行为。 – Json.org
#### 2、JSON的结构：
(1) Name/Value Pairs（无序的）：类似所熟知的Keyed list、 Hash table、Disctionary和Associative array。在Android平台中同时存在另外一个类"Bundle"，某种程度上具有相似的行为。
(2) Array（有序的）：一组有序的数据列表。
#### 对象
对象是一个无序的Name/Value Pairs集合。{ name:value, name:value, name:value ....  }
例子：{ "name":"小猪","age":20 }
#### Array
Array是值（value）的有序集合。[ value , value , value ...... ] 值（value）可以是双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）。这些结构可以嵌套。
字符串（string）是由双引号包围的任意数量Unicode字符的集合，使用反斜线转义。一个字符（character）即一个单独的字符串（character string）。 例如：\ + " \ / b f n r t u 进行转义。
例子1： Array里面包含对象(object)
[ {"id":1,"name":"小猪" ,"age”:22} , {"id":2,"name":"小猫","age”:23} ,  .......]
例子2：同样对象(object)中可以包含Array
（1）一个对象包含1个数组，2个子对象
```  
{"root":[{"id":"001","name":"小猪"},{"id":"002","name":"小猫"},{"id":"003","name":"小狗"}],
"total":3,
"success":true
}
```
（2）也可以对象嵌套子对象，子对象再嵌套数组
```  
{"calendar": 
    {"calendarlist":
            [
            {"id":"001","name":"小猪"},
            {"id":"002","name":"小猫"}
            ]
    } 
}
```
总之，格式多种多样，可以互相嵌套。
在Android中包含四个与JSON相关的类和一个Exceptions：
```  
JSONArray
JSONObject
JSONStringer
JSONTokener
JSONException
```
（1）JSONObject:
这是系统中有关JSON定义的基本单元，其包含一对儿(Key/Value)数值。
它对外部(External：应用toString()方法输出的数值)调用的响应体现为一个标准的字符串（例如：{"JSON":"Hello,World"}，最外被大括号包裹，其中的Key和Value被冒号”:”分隔）。其对于内部(Internal)行为的操作格式略微，例如：初始化一个JSONObject实例，引用内部的put()方法添加数值：new JSONObject().put("JSON", "Hello, World!")，在Key和Value之间是以逗号","分隔。
Value的类型包括：Boolean、JSONArray、JSONObject、Number、String或者默认值JSONObject.NULL object。
有两个不同的取值方法：
get(): 在确定数值存在的条件下使用，否则当无法检索到相关Key时，将会抛出一个Exception信息。
opt(): 这个方法相对比较灵活，当无法获取所指定数值时，将会返回一个默认数值，并不会抛出异常。
（2）JSONArray:
它代表一组有序的数值。将其转换为String输出(toString)所表现的形式是用方括号包裹，数值以逗号”,”分隔（例如：[value1,value2,value3]，大家可以亲自利用简短的代码更加直观的了解其格式）。这个类的内部同样具有查询行为，get()和opt()两种方法都可以通过index索引返回指定的数值，put()方法用来添加或者替换数值。
同样这个类的value类型可以包括：Boolean、JSONArray、JSONObject、Number、String或者默认值JSONObject.NULL object。
（3）JSONStringer:
根据官方的解释，这个类可以帮助快速和便捷的创建JSONtext。其最大的优点在于可以减少由于格式的错误导致程序异常，引用这个类可以自动严格按照JSON语法规则（syntaxrules）创建JSON text。每个JSONStringer实体只能对应创建一个JSON text。
根据下边的实例来了解其它相关信息：
```  
String myString = new JSONStringer().object().key("name").value("小猪").endObject().toString();
```
结果是一组标准格式的JSON text：{"name" : "小猪"}
其中的.object()和.endObject()必须同时使用，是为了按照Object标准给数值添加边界。同样，针对数组也有一组标准的方法来生成边界.array()和.endArray()。
（4）JSONTokener:
这个是系统为JSONObject和JSONArray构造器解析JSON source string的类，它可以从source string中提取数值信息。
JSONException:是JSON.org类抛出的异常信息。
下面引用一个完整的应用实例：
应用JSONObject存储Map类型数值：
```  
public static JSONObject getJSON(Map map) {
	Iterator iter = map.entrySet().iterator();
	JSONObject holder = new JSONObject();
	while (iter.hasNext()) {
		Map.Entry pairs = (Map.Entry) iter.next();
		String key = (String) pairs.getKey();
		Map m = (Map) pairs.getValue();
		JSONObject data = new JSONObject();
		try {
			Iterator iter2 = m.entrySet().iterator();
			while (iter2.hasNext()) {
				Map.Entry pairs2 = (Map.Entry) iter2.next();
				data.put((String) pairs2.getKey(),
						(String) pairs2.getValue());
			}
			holder.put(key, data);
		} catch (JSONException e) {
			Log.e("Transforming", "There was an error packaging JSON", e);
		}
	}
	return holder;
}
public static JSONObject getJSON(Map map) {
	Iterator iter = map.entrySet().iterator();
	JSONObject holder = new JSONObject();
	while (iter.hasNext()) {
		Map.Entry pairs = (Map.Entry) iter.next();
		String key = (String) pairs.getKey();
		Map m = (Map) pairs.getValue();
		JSONObject data = new JSONObject();
		try {
			Iterator iter2 = m.entrySet().iterator();
			while (iter2.hasNext()) {
				Map.Entry pairs2 = (Map.Entry) iter2.next();
				data.put((String) pairs2.getKey(),
						(String) pairs2.getValue());
			}
			holder.put(key, data);
		} catch (JSONException e) {
			Log.e("Transforming", "There was an error packaging JSON", e);
		}
	}
	return holder;
}
```