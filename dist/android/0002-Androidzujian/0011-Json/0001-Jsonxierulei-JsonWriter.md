在Android中新增了一个JSON写入类android.util.JsonWriter，使用JsonWriter可以轻松的生成JSON格式的数据，比如下面的JSON数据为：
```  
{
　　"id": 912345678901,
　　"text": "How do I write JSON on Android?",
　　"geo": null,
　　"user": {
	　　"name": "android_newb",
	　　"followers_count": 41
　　},
　　{
	　　"id": 912345678902,
	　　"text": "@android_newb just use android.util.JsonWriter!",
	　　"geo": [50.454722, -104.606667],
	　　"user": {
		　　"name": "jesse",
		　　"followers_count": 2
	　　}
　　}
}
```
上面的JSON数据在Android honeycomb上的写入代码为
```  
public void writeJsonStream(OutputStream out, List messages)
		throws IOException {
	JsonWriter writer = new JsonWriter(new OutputStreamWriter(out, "UTF-8"));
	// Android开发网提示这是UTF-8编码
	writer.setIndent(" ");
	writeMessagesArray(writer, messages);
	writer.close();
}
public void writeMessagesArray(JsonWriter writer, List messages)
		throws IOException {
	writer.beginArray();
	for (Message message : messages) {
		writeMessage(writer, message);
	}
	writer.endArray();
}
public void writeMessage(JsonWriter writer, Message message)
		throws IOException {
	writer.beginObject();
	writer.name("id").value(message.getId());
	writer.name("text").value(message.getText());
	if (message.getGeo() != null) {
		writer.name("geo");
		writeDoublesArray(writer, message.getGeo());
	} else {
		writer.name("geo").nullValue();
	}
	writer.name("user");
	writeUser(writer, message.getUser());

	writer.endObject();
}
{
	writer.beginObject();
	writer.name("name").value(user.getName());
	writer.name("followers_count").value(user.getFollowersCount());
	writer.endObject();
}
public void writeDoublesArray(JsonWriter writer, List doubles)
		throws IOException {
	writer.beginArray();
	for (Double value : doubles) {
		writer.value(value);
	}
	writer.endArray();
}
```