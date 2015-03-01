使用过SQLite数据库的童鞋对Cursor应该不陌生，如果你是搞.net开发你大可以把Cursor理解成Ado.net中的数据集合。今天特地将它单独拿出来谈，加深自己和大家对Android 中使用 Cursor 的理解。
#### 关于Cursor
在你理解和使用 Android Cursor 的时候你必须先知道关于 Cursor 的几件事情：
Cursor是每行的集合。使用moveToFirst()定位第一行。你必须知道每一列的名称。你必须知道每一列的数据类型。Cursor是一个随机的数据源。所有的数据都是通过下标取得。
#### 关于 Cursor 的重要方法：
```  
close() 关闭游标，释放资源。
copyStringToBuffer(int columnIndex, CharArrayBuffer buffer) 在缓冲区中检索请求的列的文本，将将其存储。
getColumnCount() 返回所有列的总数。
getColumnIndex(String columnName) 返回指定列的名称，如果不存在返回-1。
getColumnIndexOrThrow(String columnName) 从零开始返回指定列名称，如果不存在将抛出IllegalArgumentException 异常。
getColumnName(int columnIndex) 从给定的索引返回列名。
getColumnNames() 返回一个字符串数组的列名。getCount() 返回Cursor 中的行数。
moveToFirst() 移动光标到第一行。
moveToLast() 移动光标到最后一行。
moveToNext() 移动光标到下一行。
moveToPosition(int position) 移动光标到一个绝对的位置。
moveToPrevious() 移动光标到上一行。
```
下面来看看一小段代码：
```  
if (cur.moveToFirst() == false)
{
	//为空的Cursor
	return;
}
//访问 Cursor 的下标获得其中的数据
int nameColumnIndex = cur.getColumnIndex(People.NAME);
String name = cur.getString(nameColumnIndex);
//现在让我们看看如何循环 Cursor 取出我们需要的数据
while(cur.moveToNext())
{
	//光标移动成功
	//把数据取出
}
```
当cur.moveToNext() 为假时将跳出循环，即 Cursor 数据循环完毕。如果你喜欢用 for 循环而不想用While 循环可以使用Google 提供的几下方法：
```  
isBeforeFirst() 返回游标是否指向之前第一行的位置
isAfterLast() 返回游标是否指向第最后一行的位置
isClosed() 如果返回 true 即表示该游戏标己关闭
```
有了以上的方法，可以如此取出数据
```  
for(cur.moveToFirst();!cur.isAfterLast();cur.moveToNext()){
	int nameColumn = cur.getColumnIndex(People.NAME);
	int phoneColumn = cur.getColumnIndex(People.NUMBER);
	String name = cur.getString(nameColumn);
	String phoneNumber = cur.getString(phoneColumn);
}
```
Tip:在Android查询数据是通过Cursor类来实现的。当我们使用SQLiteDatabase.query()方法时，就会得到Cursor对象，Cursor所指向的就是每一条数据。结合ADO.net的知识可能好理解一点。Cursor位于android.database.Cursor类，可见出它的设计是基于数据库服务产生的。另外，还有几个己知的子类，分别为：
```  
AbstractCursor
AbstractWindowedCursor
CrossProcessCursor
CursorWrapper
MatrixCursor
MergeCursor
MockCursor
SQLiteCursor
```
