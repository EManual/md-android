Android中的Contentprovider机制可支持在多个应用中存储和读取数据。这也是跨应用共享数据的唯一方式。在android系统中，没有一个公共的内存区域，供多个应用共享存储数据。
Android提供了一些主要数据类型的Contentprovider，比如音频、视频、图片和私人通讯录等。可在android.provider包下面找到一些android提供的Contentprovider。可以获得这些Contentprovider，查询它们包含的数据，当然前提是已获得适当的读取权限。
如果想公开自己的数据，那么可有两种办法：
创建自己的Contentprovider，需要继承ContentProvider类； 如果你的数据和已存在的Contentprovider数据结构一致，可以将数据写到已存在的Contentprovider中，当然前提是获取写该Contentprovider的权限。比如把OA中的成员通讯信息加入到系统的联系人Contentprovider中。 
#### Contentprovider基础 
所有Contentprovider都需要实现相同的接口用于查询Contentprovider并返回数据，也包括增加、修改和删除数据。
首先需要获得一个ContentResolver的实例，可通过Activity的成员方法getContentResovler（）方法：
```  
ContentResolver cr = getContentResolver();
```
ContentResolver实例带的方法可实现找到指定的Contentprovider并获取到Contentprovider的数据。
ContentResolver的查询过程开始，Android系统将确定查询所需的具体Contentprovider，确认它是否启动并运行它。android系统负责初始化所有的Contentprovider，不需要用户自己去创建。实际上，contentprovider的用户都不可能直接访问到contentprovider实例，只能通过ContentResolver在中间代理。
#### 数据模型 
Contentprovider展示数据类似一个单个数据库表。其中：
每行有个带唯一值的数字字段，名为_ID，可用于对表中指定记录的定位；Contentprovider返回的数据结构，是类似JDBC的ResultSet，在android中，是Cursor对象。 
#### URI 
每个contentprovider定义一个唯一的公开的URI，用于指定到它的数据集。一个contentprovider可以包含多个数据集（可以看作多张表），这样，就需要有多个URI与每个数据集对应。这些URI要以这样的格式开头：
```  
content：//
```
表示这个uri指定一个contentprovider。
如果你想创建自己的contentprovider，最好把自定义的URI设置为类的常量，这样简化别人的调用，并且以后如果更新URI也很容易。android定义了CONTENT_URI常量用于URI，比如：
```  
android.provider.Contacts.Phones.CONTENT_URI
```
要注意的是上面例子中的Contacts，已经在android 2.0及以上版本不赞成使用。
#### 查询Contentprovider 
要想使用一个contentprovider，需要以下信息：
定义这个contentprovider的URI 返回结果的字段名称 这些字段的数据类型 
如果需要查询contentprovider数据集的特定记录（行），还需要知道该记录的ID的值。
#### 构建查询 
查询就是输入URI等参数，其中URI是必须的，其他是可选的，如果系统能找到URI对应的contentprovider将返回一个Cursor对象。
可以通过ContentResolver.query()或者Activity.managedQuery()方法。两者的方法参数完全一样，查询过程和返回值也是相同的。区别是，通过Activity.managedQuery()方法，不但获取到Cursor对象，而且能够管理Cursor对象的生命周期，比如当Activity暂停（pause）的时候，卸载该Cursor对象，当Activity restart的时候重新查询。另外，也可以对一个没有处于Activity管理的Cursor对象做成被Activity管理的，通过调用 Activity.startManaginCursor()方法。
类似这样：
```  
Cursor cur = managedQuery(myPerson, null, null, null, null);
```
其中第一个参数myPerson是Uri类型实例。
如果需要查询的是指定行的记录，需要用_ID值，比如ID值为23，URI将是类似：
```  
content://. . . ./23
```
android提供了方便的方法，让开发者不需要自己拼接上面这样的URI，比如类似：
```  
Uri myPerson = ContentUris.withAppendedId(People.CONTENT_URI, 23);
```
或者：
```  
Uri myPerson = Uri.withAppendedPath(People.CONTENT_URI, "23");
```
二者的区别是一个接收整数类型的ID值，一个接收字符串类型。
#### 其他几个参数：
names，可以为null，表示取数据集的全部列，或者声明一个String数组，数组中存放列名称，比如：    People._ID。一般列名都在该Contentprovider中有常量对应； 针对返回结果的过滤器，格式类似于SQL中的WHERE子句，区别是不带WHERE关键字，如果返回null表示不过滤，比如name=?； 前面过滤器的参数，是String数组，是针对前面条件中？占位符的值； 排序参数，类似SQL的ORDER BY字句，不过不需要写ORDER BY部分，比如name desc，如果不排序，可输入null。 
返回值是Cursor对象，游标位置在第一条记录之前。
下面实例适用于android 2.0及以上版本，从android通讯录中得到姓名字段：
```  
Cursor cursor = getContentResolver().query(
ContactsContract.CommonDataKinds.Phone.CONTENT_URI, null, null,null,null);
```