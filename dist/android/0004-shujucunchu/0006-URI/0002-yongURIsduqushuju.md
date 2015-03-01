现在大家已经知道，可以通过使用特定content provider提供的URIs来获取数据。因为content provider所定义的URIs对于此provider来讲是唯一的，所以这些URIs需要被很好的归档以便程序员能够调用。Android自带的providers通过定义表示URIs字符的常量来达到目的。
下面是在Android SDK里面helper类的三个URIs的定义：
```  
MediaStore.Images.Media.INTERNAL_CONTENT_URI
MediaStore.Images.Media.EXTERNAL_CONTENT_URI
Contacts.People.CONTENT_URI
```
于此同效的URI字符如下：
```  
Content://media/internal/images
Content://media/external/images
Content://contacts/people/
```
MediaStore这个provider定义了两个URI，Contacts定义了一个URI。不知你注意了没，这些常量采用了分层的定义。比如，contacts的URI是Contacts.People.CONTENT_URI。这是因为contants的数据库可能含有很多contact的记录。People是其中一个表或者集合。每个数据库记录可能都有自己的content URI，不过，所有都位于根authority下。（比如contacts provider里的contacts://contacts）
注：在Contacts.People.CONTENT_URI里，contacts是一个java包，people是包里面的接口。
有了这些URIs，从contacts provider里面获取单条people记录的代码和下面的相似：
```  
Uri peopleBaseUri = Contacts.People.CONTENT_URI;
Uri myPersonUri = peopleBaseUri.withAppendedId(Contacts.People.CONTENT_URI, 23);
//这条记录的查询
//managedQuery是Activity类的一个方法
Cursor cur = managedQuery(myPersonUri, null, null, null);
```
请注意Contacts.People.CONTENT_URI在People类里面已经被事先定义。在这个例子里，代码根据root URI，加入了具体人员的ID，然后是对managedQuery方法的调用。
作为URI查询的一部分，可以设置结果的排序，选择哪些记录以及where语句。在这里，这些参数都被设置成了null。
注：略。
列表3-20在上面例子的基础上显示了如何在People这个表里面获取具体的字段。
Listing 3-20  从content provider里面获取指针
```  
//数组指定了所返回的字段
String[] projection = new string[] {
	People._ID,
	People.Name,
	People.Number,
};
//在contacts的content provider里获取People表格的基本URI。
//比如content://contacts/people/
Uri mContactsUri = Contacts.People.CONTENT_URI;
//获取查询的最好方法；返回一个可管理的查询。
Cursor managedCursor = managedQuery(
	mContactsUri, projection,//返回的具体字段
	Null,//where语句
	Contacts.People.NAME + "ASC");//排序语句。
)
```
注意到projection只是代表字段的string数组。所以除非你知道这些字段，否则无法建立一个projection。你可以在提供URI的类里面发现这些字段名称，在这个例子中是People类。让我们看看定义在这个类里面其余的字段名：
```  
CUSTOM_RINGTONE
DISPLAY_NAME
LAST_TIME_CONTACTED
NAME
NOTES
PHOTO_VERSION
SEND_TO_VOICE_MAIL
STARRED
TIMES_CONTACTED
```
你可以通过查看SDK文档来发现更多的字段信息。
正如先前提到的，诸如contacts的数据库包含多个表，每个表是用一个类或者一个接口来描述它的字段和类型。让我们查看下android.providers.Contacts包。
你可以看到这个包含有如下类和接口：
```  
ContactMethods
Extensions
Groups
Organizations
People
Phones
Photos
Settings
```
每个类名代表着contacts.db数据库里面的一个表名，每个表负责描述自己的URI结构。此外，对应的给每个类字段接口来识别字段的名称，比如PeopleColumns。
让我们重新回顾下返回的指针：它包含零个或者多个记录。字段名，排序，类型。不过，返回的每个行包含一个默认的字段called_id，它代表行的唯一ID。