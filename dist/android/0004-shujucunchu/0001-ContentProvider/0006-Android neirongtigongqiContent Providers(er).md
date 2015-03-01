#### 读取查询所获数据Reading retrieved data
查询返回的游标对象可以用来访问结果记录集。如果你通过指定的一个ID来查询，这个集合将只有一个值。否则，它可以包含多个数值。（如果没有匹配结果，那还可能是空的。）你可以从表格中的特定字段读取数据，但你必须知道这个字段的数据类型，因为这个游标对象对于每种数据类型都有一个单独的读取方法-比如getString(),getInt(),和getFloat()。（不过，对于大多数类型，如果你调用读取字符串的方法，游标对象将返回给你这个数据的字符串表示。）游标可以让你按列索引请求列名，或者按列名请求列索引。
下面的代码片断演示了如何从前述查询结果中读取名字和电话号码：
```  
private void getColumnData(Cursor cur) {
	if (cur.moveToFirst()) {
		String name;
		String phoneNumber;
		int nameColumn = cur.getColumnIndex(People.NAME);
		int phoneColumn = cur.getColumnIndex(People.NUMBER);
		String imagePath;
		do {
			// Get the field values
			name = cur.getString(nameColumn);
			phoneNumber = cur.getString(phoneColumn);
			// Do something with the values.
		} while (cur.moveToNext());
	}
}
```
如果一个查询可能返回二进制数据，比如一个图像或声音，这个数据可能直接被输入到表格或表格条目中也可能是一个content: URI的字符串可用来获取这个数据，一般而言，较小的数据（例如，20到50K或更小）最可能被直接存放到表格中，可以通过调用Cursor.getBlob()来获取。它返回一个字节数组。
如果这个表格条目是一个content: URI，你不该试图直接打开和读取该文件（会因为权限问题而失败）。相反，你应该调用ContentResolver.openInputStream()来得到一个InputStream对象，你可以使用它来读取数据。
修改数据Modifying Data
保存在内容提供器中的数据可以通过下面的方法修改：
增加新的记录
为已有的记录添加新的数据
批量更新已有记录
删除记录
所有的数据修改操作都通过使用ContentResolver方法来完成。一些内容提供器对写数据需要一个比读数据更强的权限约束。如果你没有一个内容提供器的写权限，这个ContentResolver方法会失败。
增加记录Adding records
想要给一个内容提供器增加一个新的记录，第一步是在ContentValues对象里构建一个键-值对映射，这里每个键和内容提供器的一个列名匹配而值是新记录中那个列期望的值。然后调用ContentResolver.insert()并传递给它提供器的URI和这个ContentValues映射图。这个方法返回新记录的URI全名-也就是，内容提供器的URI加上该新记录的扩展ID。你可以使用这个URI来查询并得到这个新记录上的一个游标，然后进一步修改这个记录。下面是一个例子：
```  
ContentValues values = new ContentValues();
// Add Abraham Lincoln to contacts and make him a favorite.
values.put(People.NAME, "Abraham Lincoln");
// 1 = the new contact is added to favorites
// 0 = the new contact is not added to favorites
values.put(People.STARRED, 1);
Uri uri = getContentResolver().insert(People.CONTENT_URI, values);
```
#### 增加新值Adding new values
一旦记录已经存在，你就可以添加新的信息或修改已有信息。比如，上例中的下一步就是添加联系人信息-如一个电话号码或一个即时通讯IM或电子邮箱地址-到新的条目中。
在联系人数据库中增加一条记录的最佳途径是在该记录URI后扩展表名，然后使用这个修正的URI来添加新的数据值。为此，每个联系人表暴露一个CONTENT_DIRECTORY常量的表名。下面的代码继续之前的例子，为上面刚刚创建的记录添加一个电话号码和电子邮件地址：
```  
Uri phoneUri = null;
Uri emailUri = null;
// Add a phone number for Abraham Lincoln. Begin with the URI for
// the new record just returned by insert(); it ends with the _ID
// of the new record, so we don't have to add the ID ourselves.
// Then append the designation for the phone table to this URI,
// and use the resulting URI to insert the phone number.
phoneUri = Uri.withAppendedPath(uri, People.Phones.CONTENT_DIRECTORY);
values.clear();
values.put(People.Phones.TYPE, People.Phones.TYPE_MOBILE);
values.put(People.Phones.NUMBER, "1233214567");
getContentResolver().insert(phoneUri, values);
// Now add an email address in the same way.
emailUri = Uri.withAppendedPath(uri, People.ContactMethods.CONTENT_DIRECTORY);
values.clear();
// ContactMethods.KIND is used to distinguish different kinds of
// contact methods, such as email, IM, etc. 
values.put(People.ContactMethods.KIND, Contacts.KIND_EMAIL);
values.put(People.ContactMethods.DATA, "test@example.com");
values.put(People.ContactMethods.TYPE, People.ContactMethods.TYPE_HOME);
getContentResolver().insert(emailUri, values);
```
你可以通过调用接收字节流的ContentValues.put()版本来把少量的二进制数据放到一张表格里去。这对于像小图标或短小的音频片断这样的数据是可行的。但是，如果你有大量二进制数据需要添加，比如一张相片或一首完整的歌曲，则需要把该数据的content: URI放到表里然后以该文件的URI调用ContentResolver.openOutputStream() 方法。（这导致内容提供器把数据保存在一个文件里并且记录文件路径在这个记录的一个隐藏字段中。）