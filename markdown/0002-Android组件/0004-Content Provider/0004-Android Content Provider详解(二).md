#### 读取返回的数据 
如果在查询的时候使用到ID，那么返回的数据只有一条记录。在其他情况下，一般会有多条记录。和JDBC的ResultSet类似，需要操作游标遍历结果集，在每行，再通过列名获取到列的值，可以通过getString()、getInt()、getFloat()等方法获取值。比如类似下面：
```  
while (cursor.moveToNext()) {
	builder.append(
	cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME))).append("-");
}
```
和JDBC中不同，没有直接通过列名获取列值的方法，只能先列名获取到列的整型索引值，然后再通过该索引值定位获取列的值。
#### 编辑数据 
可以通过contentprovider实现以下编辑功能：
增加新的记录； 在已经存在的记录中增加新的值； 批量更新已经存在的多个记录； 删除记录。 
所有的编辑功能都是通过ContentResolver的方法实现。一些Contentprovider对权限要求更严格一些，需要写的权限，如果没有会报错。
#### 增加记录 
要想增加记录到contentprovider，首先，要在ContentValues对象中设置类似map的键值对，在这里，键的值对应contentprovider中的列的名字，键值对的值，是对应列希望的类型。然后，调用ContentResolver.insert()方法，传入这个ContentValues对象，和对应Contentprovider的URI即可。返回值是这个新记录的URI对象。这样你可以通过这个URI获得包含这条记录的Cursor对象。比如：
```  
ContentValues values = new ContentValues();
values.put(People.NAME, "Abraham Lincoln");
Uri uri = getContentResolver().insert(People.CONTENT_URI, values);
```
#### 在原有记录上增加值 
如果记录已经存在，可在记录上增加新的值，或者编辑已经存在的值。
首先要过去到原来的值对象，然后要清除原有的值，然后像上面增加记录一样即可：
```  
Uri uri=Uri.withAppendedPath(People.CONTENT_URI, "23");
Uri phoneUri = Uri.withAppendedPath(uri, People.Phones.CONTENT_DIRECTORY);
values.clear();
values.put(People.Phones.TYPE, People.Phones.TYPE_MOBILE);
values.put(People.Phones.NUMBER, "1233214567");
getContentResolver().insert(phoneUri, values);
```
#### 批量更新值 
批量更新一组记录的值，比如NY改名为Eew York。可调用ContenResolver.update()方法。
#### 删除记录 
如果是删除单个记录，调用ContentResolver.delete()方法，URI参数，指定到具体行即可。
如果是删除多个记录，调用ContentResolver.delete()方法，URI参数指定Contentprovider即可，并带一个类似SQL的WHERE子句条件。这里和上面类似，不带WHERE关键字。
#### 创建自己的Contentprovider 
创建contentprovider，需要：
设置存储系统。大多数contentprovider使用文件或者SQLite数据库，不过你可以用任何方式存储数据。android提供SQLiteoOpenHelper帮助开发者创建和管理SQLiteDatabase。 继承ContentProvider，提供对数据的访问。 在manifest文件中声明contentprovider。 
继承ContentProvider类，必须定义ContentProvider类的子类，需要实现如下方法：
```  
query()
insert()
update()
delete()
getType()
onCreate()
```