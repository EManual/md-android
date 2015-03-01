#### 1. SQLiteDatabase
操作SQLite数据库的类。可以执行SQL语句，对数据库进行增、删、查、改的操作。也可以进行transaction的控制。很多类对数据库的操作最终都是通过SQLiteDatabase实例来调用执行的。
需要注意的是，数据库对于一个应用来说是私有的，并且在一个应用当中，数据库的名字也是惟一的。
#### 2. SQLiteOpenHelper
创建数据库和数据库版本管理的辅助类。这是一个抽象类，所以我们一般都有一个SQLiteOpenHelper子类，需要继承实现。
· void onCreate(SQLiteDatabase db) 
在数据库第一次生成的时候会调用这个方法，一般我们在这个方法里边生成数据库表。
· void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) 
当数据库需要升级的时候，Android系统会主动的调用这个方法。一般我们在这个方法里边删除数据表，并建立新的数据表，当然是否还需要做其他的操作，完全取决于应用的需求。
而void onOpen(SQLiteDatabase db) 则可以选择重写。
同时此类还有3个synchronized方法：
```  
void close() 
SQLiteDatabase getReadableDatabase() 
SQLiteDatabase getWritableDatabase() 
```
#### 3. Cursor
游标。要注意这是一个接口。但很多时候我不需要知道它具体是哪个子类，直接按照API Doc里面提供的方法调用就行了。通过Cursor，我们可以对从数据库查询出来的结果集进行随机的读写访问。对于数据库的查询结果，一般是由子类SQLiteCursor返回的。
#### 4. ContentProvider
因为在Android系统里面，数据库是私有的。一般情况下外部应用程序是没有权限读取其他应用程序的数据。如果你想公开你自己的数据，你有两个选择：你可以创建你自己的内容提供器（一个ContentProvider子类）或者你可以给已有的提供器添加数据-如果存在一个控制同样类型数据的内容提供器且你拥有写的权限。而外界根本看不到，也不用看到这个应用暴露的数据在应用当中是如何存储的，或者是用数据库存储还是用文件存储，还是通过网上获得，这些一切都不重要，重要的是外界可以通过这一套标准及统一的接口和程序里的数据打交道，可以读取程序的数据，也可以删除程序的数据，当然，中间也会涉及一些权限的问题。
而以下方法是需要在子类实现的：
```  
boolean onCreate()  Called when the provider is being started.
Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,String sortOrder)  通过Uri进行查询，返回一个Cursor。
Uri insert(Uri url, ContentValues values)  将一组数据插入到Uri 指定的地方，返回新inserted的URI。
int update(Uri uri, ContentValues values, String where, String[] selectionArgs)  更新Uri指定位置的数据，返回所影响的行数。
int delete(Uri url, String where, String[] selectionArgs)  删除指定Uri并且符合一定条件的数据，返回所影响的行数。
String getType (Uri uri)  获取所查询URI的MIME类型，如果没有类型则返回null。
```
当我们不需要把数据提供给第三方应用程序使用的话，可以不实现Content Provider。
#### 5. ContentResolver
外界的程序通过ContentResolver接口可以访问ContentProvider提供的数据，在Activity当中通过getContentResolver()可以得到当前应用的ContentResolver实例。ContentResolver提供的接口和ContentProvider中需要实现的接口对应，具体可以查看API Doc，不过可以发现ContentResolver里面的方法很多都是final或者static的。在实际应用中，我们很少实现ContentResolver抽象类，更多时候通过getContentResolver()从一个Activity或其它应用程序组件的实现里获取一个ContentResolver：
```  
ContentResolver cr = getContentResolver();
```
然后你可以使用这个ContentResolver的方法来和你感兴趣的任何内容提供器交互。
当初始化一个查询时，Android系统识别查询目标的内容提供器并确保它正在运行。系统实例化所有的ContentProvider对象；你从来不需要自己做。事实上，你从来不会直接处理ContentProvider对象。通常，对于每个类型的ContentProvider只有一个简单的实例。但它能够和不同应用程序和进程中的多个ContentProvider对象通讯。进程间的交互通过ContentResolver和ContentProvider类处理。
#### 6. URI的说明
每个Content Provider都需要公开一个URI来标识它的数据集。一个控制多个数据集（多个表）的Content Provider为每一个数据集暴露一个单独的URI。而URIs都以字符串"content://"开始。这个content:形式表明了这个数据正被一个Content Provider控制着。
将其分为A，B，C，D 4个部分：
A：标准前缀，用来说明一个Content Provider控制这些数据，无法改变的；
B：URI的权限部分，它定义了是哪个Content Provider提供这些数据。对于第三方应用程序，为了保证URI标识的唯一性，它必须是一个完整的、小写的   类名。这个标识在<provider> 元素的 authorities属性中说明：
```  
<provider
	name=".TransportationProvider"
	authorities="com.example.transportationprovider"
	. . .  >
```
C：用来判断请求数据类型的路径。Content Provider使用这些路径来确定当前需要生什么类型的数据。这可以是0或多个段长。如果内容提供器只暴露了一种数据类型（比如，只有火车），这个分段可以没有。如果提供器暴露若干类型，包括子类型，那它可以是多个分段长-例如，提供"land/bus", "land/train", "sea/ship", 和"sea/submarine"这4个可能的值。
D：被请求的特定记录的ID。如果URI中包含，表示需要获取的记录的ID；如果没有ID，就表示返回全部，这个分段和尾部的斜线会被忽略；
现在应该明白ContentResolver是通过URI来查询ContentProvider中提供的数据。除了URI以外，还必须知道需要获取的数据段的名称，以及此数据段的数据类型。如果你需要获取一个特定的记录，你就必须知道当前记录的ID，也就是URI中D部分。
现在我们来创建一个ContentProvider。按照官方的说法，“要创建一个内容提供器，你必须：
· 建立一个保存数据的系统。大多数Content Provider使用Android的文件储存方法或SQLite数据库来存放它们的数据，但是你可以用任何你想要的方式来存放数据。实现SQLiteOpenHelper类来帮助你创建一个数据库以及SQLiteDatabase类来管理它。
· 扩展ContentProvider类来提供数据访问接口。
· 在清单manifest文件中为你的应用程序声明这个Content Provider（AndroidManifest.xml）。
1.设计好一个实体类，定义好需要公开的变量，column，URI等
2.创建内部类实现SQLiteOpenHelper，用于创建更新数据表，以及后面的获取SQLiteDatabase引用
3.编写ContentProvider子类或者其他封装数据库操作的自定义类，包含上面的内部类
4.在AndroidManifest.xml的application element里面添加子element provider (和element activity同一层)，声明这个Content Provider。