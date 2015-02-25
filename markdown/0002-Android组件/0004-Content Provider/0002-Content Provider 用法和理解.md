#### 1、用途 
Content Provider存储(store)和提取(retrieve)数据，并且供所有的应用使用。这是应用之间共享数据(share data)的唯一方式，除此之外，再没有别的通用存储区使得所有的应用可以访问。 
Android包含一些Content Provider，提供公共的数据类型，比如audio、video、image、个人联系信息等）。通过这些Content Provider可以查询数据，有时也需要获取相应的读取权限。 
如果要将数据公开，有两种方式：
1）扩展Content Provder，定制一个Provider；
2）将数据添加到已有的Content Provider（控制相同类型的数据，并且有权限写入）。 
#### 2、构成 
三个关键类: Content Resolver，Cursor （表示返回结果），URI（标识Provider,作为Resovler的查询参数）。 
#### 内容解析器(Content Provider) 
Content Provider如何存储数据取决于其设计者。但所有的Content Provider实现一个通用的接口用于查询(query)并返回结果，当然也有增加(add)、修改(alter)和删除(delete)数据的通用接口。 
客户端一般通过Content Resovler(内容解析器）间接地获取内容，在Activity中可以通过
```  
ContentResolver cr = getContentResovler(); 
```
获取内容解析器。 
可以通过Content Resover与任意Content Provider进行交互。 
查询时，Android系统识别其查询目标Content Provider,确保其启动和运行。正如诸多框架中服务引擎(Service Engine)初始化所有的服务(Service)，系统（可视为数据引擎Data Engine）初始化所有的Content Provider，不需要手动初始化，事实上，也不应该直接去操作Content Provider。 
一般，每个ContentProvider都只有一个实例（单例模式），但可以与多个在不同应用或进程中的Content Resovler进行通信。进程间的通信可以通过ContentResovler和ContentProvider进行。 
#### 数据模型(data model) 
Content provider将其数据以数据库中简单的表模型展示，一行表示一条记录，一列表示数据的某种类型和含义。 
每条记录都有一个数值_ID字段，唯一地标识一条记录。ID可用于匹配相关联的表。比如，在一个表中找到联系人的电话号码，在另一张表中找到这个联系人的头像。 
查询返回的结果以Cursor（游标，有点文件指针的味道），可以在记录或字段之间游动。其有专门的方法读取各种类型的数据，所以读取一个字段之前，应当知道字段的数据类型。 
#### 统一资源标识（Uri) 
每个Content Provider使用一个公开的URI唯一地标识其数据集。持有多个数据集（多张表）的ContentProvider可 
以为每个数据集提供一个独立的URI。所有Provider的URI以"content://";作为前缀，犹如Web Resource的URI以"http://";作为前缀。content: 方案(scheme)表明数据是由content provider提供。 
定义一个Content Provider，最好也定义一个常量作为其URI，可以简化客户端的代码，并在以后的更新中保持简洁。Android定义(define)许多CONTENT_URI常量用于标识自带的Content Provider。比如
```  
android.provider.Contacts.Phones.CONTENT_URI
android.provider.Contacts.Photos.CONTENT_URI
```
等。 
URI常量在与Content Provider的所有交互中使用。每个ContentResolver以URI作为第一个参数（可见其很重要）。这个URI标识了ContentResovler与哪个Content Provider对话(talk)，并将其哪一张table作为目标（target)。 
#### 3、用法 
#### 3.1) 查询(query)Content Provider 
查询content provide至少需要三部分信息： 
```  
Provider的URI（查询哪一张表） 
数据字段名（返回那些列） 
数据字段类型（可以使用相应的方法） 
如果是查询某条记录，也需要指定记录的ID。 
```
#### 构建查询（Making the query） 
ContentResovler.query()和Activity.mangagedQuery()接收同样的参数，返回同样的结果(Cursor)。 
不同的是后者将Cursor的生命周期交给Activity自动管理，一般可以通过Activity.startManagingCursor()和Activity.stopManagingCursor来决定是否将Cursor的生命周期交给Activity管理。 
第一个参数URI指定Content Provider和Data Set，如果还要指定数据集中的某条记录，需要在URI的尾部添加记录ID，这可用ContentUris.withAppendedId()和Uri.withAppendedPath()来实现。 
奇特的现象，Uri在android.net包中，Cursor在android.database包中。 
第一个参数指定返回的表，不能为空。 
第二个参数指定返回的列集。null表示返回所有的列。 
第三个参数指定返回的行集。null表示返回所有的行。相当于sql的where子句。（除非URI限定） 
第四个参数是为第三个参数中出现？提供实参。（Arugments 实参，Parameters 形参） 
第五个参数指定返回的数据集的排序方式。null表示原序返回。相当于sql的order by子句。 
query操作可以分为两大类型：1）关系操作 2）非关系操作。 
两者的执行时间的分水岭是select，即关系操作在select之前执行，非关系操作在select之后执行。 
关系操作的执行先后顺序分别是：
```  
1) from join 全集 
2) where 过滤全集 
3) group by 子集 
4) having 过滤子集 
```
非关系操作的执行先后顺序分别是：
```  
1) order by 排序 
2) limit 分页 
3) distinct 去重 
4) (attr1,...) 选列 
```
关系操作实质即声明式操作（不改变集合），而非关系操作实质即命令式（过程式）操作（改变集合）。可以将数据集理解为状态集合（既定事实），过滤条件理解为动作集合（推理），结果集理解为事件集合（推演事实）。 
接口定义常量，实现类实现这些接口，即复用这些常量。 
#### 查询结果（What a query returns） 
可以使用Cursor读取结果集，但是增、改、删等操作，需要使用Content Resovler。 
读取检索到的数据(Reading retrieved data) 
查询返回的Cursor用于访问结果集。（Cursor相当于一个文件指针，而结果集相当于一个连接到文件的流）。 
Cursor指向的集合，可能包含0、1或多个值，注意即便集合中只有一个元素，也是一个集合；没有元素，也是一个集合（空集）。 
当结果集的元素是content:URI时，不应该直接尝试打开并读取，而应是创建(ContentResolver.openInputStream())一个InputStream, 从中读取数据。 
#### 3.2) 修改(modify)Content Provider 
把信息存储在ContentValues(哈希表）中，而后将其和URI作为参数，传入给ContentResovler，ContentResovler实现将信息持久化在ContentProvider中。 
```  
1、增加一条新纪录 
2、增加新的值到已有的记录 
3、批量更新记录 
4、删除记录 
```
所有的数据修改有Content Resolver的方法实现。一些Content Provider需要比如读数据更严格的权限来写数据。 
如果你没有写得权限，Content Resovler的方法将失效。 
#### 增加一条记录 
为了往Content Provicder增加一条记录，第一步在ContentValues中创建一个key-value的映射（map)，key匹配Content Provider的列名，value表示在该列中对应的值。随后，调用ContentResovler.insert()方法，传入Provider的URI以及ContentValues映射。该方法返回这条新记录的URI，即provider的URI加上新纪录的ID。可以使用这条新纪录的URI查询并获取其Cursor，进而修改这条记录。 
就是在一张表中增加一条记录，一条记录用一个哈希表表示。 
#### 增加一些新值
如果一条记录存在，可以增加或修改已有的信息。比如可以增加联系人信息—电话号码、IM、Email等。 
往Contacts数据库的一条记录增加新值最好的办法是，在表名后添加该条记录的URI，使用完善后的URI增加新值。 
CONTENT_DIRECTORY 
批量更新记录(Batch updating records) 
删除一条记录(Deleting a record) 
#### 3.3) 创建一个Content Provider 
Content Provider: CP Service Provider: SP（一般命名为ServiceEngine) 
为了创建一个Content Provider, 需要如下步骤： 
a) 创建一个数据存储系统。大多数Content Provider使用Android的文件存储方法或SQLite数据库赖保存它们的数据，但也可以任何别的方式存储数据。Android提供SQLiteOpenHelper类来创建数据库并提供SQLiteDatabase管理它。 
b) 扩展ContentProvider类访问该数据。 
c) 在AndroidManifest.xml中配置Content Provider。 
#### 扩展ContentProvider类 
可以定义Content Provider的一个子类，便捷地使用ContentResolver和Cursor数据暴露给其他类。原则上，这意味者必须实现ContentProvider声明的六个接口。 
```  
query() 
insert() 
udpate() 
delte() 
getType()  Multipurpose Internet Mail Extensions, “多用途”因特网邮件扩展媒体。 
onCreate() 
```
对于数据data的循环操作用迭代iterate，对于动作action的循环操作用递归recursion。 
Cursor相当于一个文件指针(fiel pointer)，而结果集(result set)相当于一个连接到文件的流(stream)。 Iterator相对于Data Structure，相当于Cursor相对于Database。 
query()方法返回Cursor对象用于迭代请求到的数据。Cursor本身是一个接口，但是Android提供一些制作好的Cursor对象。比如SQLiteCursor可以迭代SQLite数据库中的数据，可以通过调用SQLDatabase对象的query()方法获取SQLite对象。也有其他的Cursor实现，如MatrixCursor用于迭代不在数据库中的数据。 
由于这些ContentProvider的方法可以被多个ContentResovler对象在不同的进程(process)或线程(thread)中调用，它们必须以线程安全的方式实现。 
方便起见，可以调用ContentResovler.notifyChange()通知listeners数据已经发生变化。 
除了定义子类本身，也需要做些简化客户端的工作，使得该类更易于使用。 
a) 定义表名：定义一个公开的静态常量CONTENT_URI,表示该conent provider可以处理的content:URI字符串。你也许可以定义一个唯一的字符串来表示这个值。但最好的解决方式是使用全称（fully-qulified)的类名。 
比如： 
```  
public static final Uri CONTENT_URI = Uri.parse("content://com.example.codelab.transportationprovider"); 
```
如果provider也有子表，也给每个子表定义常量CONTENT_URI。这些子表的URI包含content provider的标识，并通过其path进行区分。
例如： 
```  
content://com.example.codelab.transportationprovider/train 
content://com.example.codelab.transportationprovider/air/domestic 
content://com.example.codelab.transportationprovider/air/international 
```
b) 定义列名：定义Content Provider返回给Client的列名。如果使用已投入使用的数据库，列名一般与数据库的列名一致。定义public static 字符串常量，client可在查询或更新用来指定列。 
确保包含一个integer列命名为_id用于标识一条记录。应当有这个字段（不管是否有其它字段），它在所有记录之间是独一无二的。如果你使用SQLite数据库，_ID字段应该这样的定义：integer primary key autoincrement 
这个autoincrement描述是可选的。如果没有它，SQLite会在该列的已有的最大值之上，自增ID计数字器段的值到下一个数。如果你删除上一列，增加的下一列的ID与已经删除的记录ID相同。Autoincrement使得SQLite自增到下一个最大值，不管是否删除前面的记录。 
c) 每一列的数据类型准确说明。客户端需要这些数据来读取数据。 
d) 如果要处理一个新的数据类型，需要定义一个新的MIME类型，在ContentProvider.getType()中返回。 
类型部分依赖于提交到getType()content: URI是否限制请求某条具体的记录。 
存在单条记录和多条记录的MIME类型。
e) 如果byte数据很大，比如大图像文件，不适合放在表中。可以将其conent:UR字符串放在表中。命名为_data字段。 
对于一个私有的content provider实现，可以参考NodePadProvider类。 
声明Content Provider 
```  
<provider 
	android:name="com.example.autos.AutoInfoProvider"
	android:authorities="com.example.autos.autoinfoprovider"
    . . . />
</provider> 
```
autority是标识provider，不是path。 
#### Content URI Summary 
包含4个部分： 
1）标准前缀指定数据由一个content provider控制；（固定）conent://   http:// 
2）URI的管理者部分：标识content provider, 一般是消息的类名全称（唯一）。www.baidu.com 
3）Path: 哪一类型的数据被请求。/path
4) ID： 那一条记录。/path/id