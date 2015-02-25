在Android平台上可以操作数据库，这是第一次接触Android时的惊艳之一。在Android平台上，绑定了SQLite数据库，这个数据库系统也是极具性格的，它的最大的应用场景是嵌入式系统。
如果有JDBC的经验，那么在这里会容易的多。Android中操作数据库首先要通过一个类：android.database.sqlite.SQLiteOpenHelper。它封装了如何打开一个数据库，其中当然也包含如果数据库不存在 就创建这样的逻辑。
看一个例子：
```  
public class DatabaseHelper extends SQLiteOpenHelper {
	private static final String DATABASE_NAME = "com.roiding.simple.note";
	private static final int DATABASE_VERSION = 1;
	private static final String NOTES_TABLE_NAME = "notes";
	DatabaseHelper(Context context) {
		super(context, DATABASE_NAME, null, DATABASE_VERSION);
	}
	@Override
	public void onCreate(SQLiteDatabase db) {
		db.execSQL("CREATE TABLE " + NOTES_TABLE_NAME
				+ " (id integer primary key autoincrement, name text);");
	}
	@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		db.execSQL("DROP TABLE IF EXISTS notes");
		onCreate(db);
	}	
}
```
这里面，如下的语句需要解释：
```  
super(context, DATABASE_NAME, null, DATABASE_VERSION)
```
数据库连接的初始化，中间的那个null，是一个CursorFactory参数，没有仔细研究这个参数，暂时置空吧。
public void onCreate(SQLiteDatabase db)
这里面的onCreate是指数据库onCreate时，而不是DatabaseHelper的onCreate。也就是说，如果已经指定 database已经存在，那么在重新运行程序的时候，就不会执行这个方法了。要不然，岂不是每次重新启动程序都要重新创建一次数据库了！在这个方法中， 完成了数据库的创建工作。也就是那个execSQL()方法。
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
在程序的开发维护过程中，数据库的结构可能会有变化，那么这个方法就有用处了。在DatabaseHelper这个对象一创建时，就已经把参数 DATABASE_VERSION传入，这样，如果Android发现此版本与现有版本不一致，就会调用这个onUpgrate方法。于是，可以在这里面 实现一些数据的upgrade工作，比如说创建一个临时表，将数据由临时表中转到新的表结构中。需要注意的是，这里面的onUpgrade是在版本不一致 时调用，也就是说不管当前需要的版本高于现有版本还是低于现有版本，都会出发这个方法，类似的这种情况，就需要对oldVersion和 newVersion进行判断之后再决定使用什么策略来更新数据。
在Android中，数据库存放在 /data/data/PACKAGE_NAME/databases 目录下。
接下来就可以使用这个Helper来操作数据库了，操作数据库也就无非是增、删、改、查。先看一个增的例子：
```  
public static void insert(Context context, String s) {
	DatabaseHelper mOpenHelper = new DatabaseHelper(context);
	String table = "notes";
	String nullColumnHack = "id";
	ContentValues values = new ContentValues();
	values.put("name", "www.roiding.com");
	long id = mOpenHelper.getReadableDatabase().insert(table,
			nullColumnHack, values);
	mOpenHelper.getReadableDatabase().close();
}
DatabaseHelper mOpenHelper = new DatabaseHelper(context);
```
如果和JDBC例子的话，这一步貌似就像是获得了一个Statement，用它就可以操作数据库了。
ContentValues values = new ContentValues();
Android在向数据库中插入数据的时候，要求数据存放到ContentValues中，这里面的ContentValues其实就是一个 Map，Key值是字段名称，Value值是字段的值。这样，也许你会发现一个问题，那数据类型怎么办？其实在SQLite数据库中就是没有数据类型的， 一切都是字符串。
mOpenHelper.getReadableDatabase().insert(table,nullColumnHack, values);
将数据入库，注意这里面有一个nullColumnHack，看文档是和所有字段都是空的记录有关系，我没有去实验他具体的效果，只是随便给他一个字段名称。
再看一个查的例子：
```  
public static void select(Context context) {
	DatabaseHelper mOpenHelper = new DatabaseHelper(context);
	String table = "notes";
	String[] columns = new String[] { "id", "name" };
	String selection = "id>? and name<>?";
	String[] selectionArgs = new String[] { "0", "roiding.com" };
	String groupBy = null;
	String having = null;
	String orderBy = "id desc";
	String limit = "1";
	Cursor c = mOpenHelper.getReadableDatabase().query(table, columns,
			selection, selectionArgs, groupBy, having, orderBy, limit);
	c.moveToFirst();
	for (int i = 0; i < c.getCount(); i++) {
		String s = c.getString(1);
		c.moveToNext();
	}
	c.close();
	mOpenHelper.getReadableDatabase().close();
}
DatabaseHelper mOpenHelper = new DatabaseHelper(context);
```
于前文中的相同
mOpenHelper.getReadableDatabase().query();
通过mOpenHelper.getReadableDatabase()，会得到一个SQLiteDatabase类型的只读的数据库连接，在这里直接调用了他的query方法。这个query方法相对复杂，因为他将一个完整的SQL语句拆成了若干个部分：
```  
table：表名。相当于SQL的from后面的部分。那如果是多表联合查询怎么办？那就用逗号将两个表名分开，拼成一个字符串作为table的值。
columns：要查询出来的列名。相当于SQL的select后面的部分。
selection：查询条件，相当于SQL的where后面的部分，在这个语句中允许使用“?”，也就是说这个用法和JDBC中的PreparedStatement的用法相似。
selectionArgs：对应于selection的值，selection有几个问号，这里就得用几个值。两者必须一致，否则就会有异常。
groupBy：相当于SQL的group by后面的部分
having：相当于SQL的having后面的部分
orderBy：相当于SQL的order by后面的部分，如果是倒序，或者是联合排序，可以写成类似这样：String orderBy = “id desc, name”;
limit：指定结果集的大小，它和Mysql的limit用法不太一样，mysql可以指定从多少行开始之后取多少条，例如“limit 100,10”，但是这里只支持一个数值。
```
c.moveToFirst();
这一句也比较重要，如果读取数据之前，没有这一句，会有异常。
c.getString(1);
与JDBC一致了，Android不支持按字段名来取值，只能用序号。
再看一个删除和修改的例子：
```  
public static void delete(Context context) {
	DatabaseHelper mOpenHelper = new DatabaseHelper(context);
	String table = "notes";
	String selection = "id>? and name<>?";
	String[] selectionArgs = new String[] { "0", "roiding.com" };
	String whereClause = selection;
	String[] whereArgs = selectionArgs;
	mOpenHelper.getWritableDatabase().delete(table, whereClause, whereArgs);
	mOpenHelper.getWritableDatabase().close();
}
```
有了上面的基础这里就容易理解了，这里的whereClause相当于前面的selection，whereArgs相当于前面的selectionArgs。
```  
public static void update(Context context) {
	DatabaseHelper mOpenHelper = new DatabaseHelper(context);
	String table = "notes";
	String selection = "id>? and name<>?";
	String[] selectionArgs = new String[] { "0", "roiding.com" };
	String whereClause = selection;
	String[] whereArgs = selectionArgs;
	ContentValues values = new ContentValues();
	values.put("name", "www.roiding.com");
	mOpenHelper.getWritableDatabase().update(table, values, whereClause,
			whereArgs);
	mOpenHelper.getWritableDatabase().close();
}
```
这个update的用法，综合select和delete就可以理解。
#### 注意：
Cursor和Databases要及时关闭，不然也会有异常。
getWritableDatabase()和getReadableDatabase()在当前的Android版本中貌似可以通用，像上面的insert，用的就是getReadableDatabase。
在真实的应用中，会对上面这些基本操作做更高一级的抽象和封装，使之更容易使用。在select时，除了用上述的方法，将分段的SQL语句传进去之外，Android还支持一种方法：使用SQLiteQueryBuilder。如果使用的是上述的分段SQL语句的方法，在Android的内部实现中，也是先将分段的SQL使用SQLiteQueryBuilder的静态方法来生成一个真正的SQL的，而且，我没有看出来使用 SQLiteQueryBuilder的优势。