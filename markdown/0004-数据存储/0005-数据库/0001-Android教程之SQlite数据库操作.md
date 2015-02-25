SQlite API:
android.database.sqlite.SQLiteOpenHelper
public SQLiteOpenHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version)  
Create a helper object to create, open, and/or manage a database. 
The database is not actually created or opened until one of getWritableDatabase() or getReadableDatabase() is called.
直到调用getWritableDatabase()函数或者getReadableDatabase()时才会创建或者打开数据库。（如果数据库没有创建，那么会先创建数据库）
Parameters  
context	to use to open or create the database
name	of the database file, or null for an in-memory database
factory	to use for creating cursor objects, or null for the default
version	number of the database (starting at 1); if the database is older, onUpgrade(SQLiteDatabase, int, int) will be used to upgrade the database
A helper class to manage database creation and version management. 
SQLiteOpenHelper类是管理数据库生成和数据库版本建立的辅助类。
You create a subclass implementing onCreate(SQLiteDatabase), onUpgrade(SQLiteDatabase, int, int) and optionally onOpen(SQLiteDatabase), and this class takes care of opening the database if it exists, creating it if it does not, and upgrading it as necessary. Transactions are used to make sure the database is always in a sensible state
SQLiteOpenHelper，如果这个数据库存在，那么SQLiteOpenHelper负责管理数据库。
如果数据库不存在，那么SQLiteOpenHelper会建立一个数据库。
如果需要的话，升级数据库。
```  
private static final String DB_NAME = "CartDB.db";
private static final int DB_VERSION = 2;
private static final String TABLE_NAME_1 = "MyOrder";
private static final String TABLE_NAME_2 = "OrderLine";
private static class DatabaseHelper extends SQLiteOpenHelper {
	DatabaseHelper(Context context) {
		super(context, DB_NAME, null, DB_VERSION);
	}
	@Override
	public void onCreate(SQLiteDatabase db) {
		db.execSQL("CREATE TABLE " + TABLE_NAME_1 + " (" + "order_no
				+ " text not null, " + "type" + " text not null, " + "desc
				+ " text" + ");");
		db.execSQL("CREATE TABLE " + TABLE_NAME_2 + " (" + "order_no
				+ " text not null, " + "item_no" + " text not null, " + "QTY
				+ " text" + ");");
	}
	@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
	}
}
```
1.首先得新建数据库的代理类，通过这个类得到具体数据库的代理类。
```  
mOpenHelper = new DatabaseHelper(this);
```
这个DatabaseHelper是继承SQLiteOpenHelper
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
2.由于操作的是SQLiteDatabase数据，故得到一个SQLiteDatabase类，这个类里边的方法可以对数据库进行具体的操作。
建立数据库：
```  
mOpenHelper = new DatabaseHelper(v.getContext());
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
String sql = "create table Student(" + "stud_no text not null,
		+ "stud_name text );";
try {
	db.execSQL(sql);
	setTitle("create table ok!");
} catch (SQLException e) {
	Log.e("ERROR", e.toString());
	setTitle("create table Error!");
}
```
drop数据库：
```  
mOpenHelper = new DatabaseHelper(v.getContext());
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
String sql = "drop table Student";
try {
	db.execSQL(sql);
	setTitle("drop table ok!");
} catch (SQLException e) {
	Log.e("ERROR", e.toString());
	setTitle("drop table Error!");
}
```
插入语句：
采用sql语句进行插入：
```  
mOpenHelper = new DatabaseHelper(v.getContext());
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
String sql_1 = "insert into Student (stud_no, stud_name) values('S108', 'Lily Chen');";
String sql_2 = "insert into Student (stud_no, stud_name) values('S201', 'Tom Kao');";
String sql_3 = "insert into Student (stud_no, stud_name) values('S333', 'Peter Rabbit');";
try {
	db.execSQL(sql_1);
	db.execSQL(sql_2);
	db.execSQL(sql_3);
	setTitle("insert records ok!");
} catch (SQLException e) {
	Log.e("ERROR", e.toString());
}
```
采用第二种方便的方法进行插入：（采用contentValues的方法）
```  
mOpenHelper = new DatabaseHelper(v.getContext());
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
ContentValues cv = new ContentValues();
cv.put("stud_no", "S108");
cv.put("stud_name", "Lily Chen");
db.insert("Student", null, cv);
cv = new ContentValues();
cv.put("stud_no", "S201");
cv.put("stud_name", "Tom Kao");
db.insert("Student", null, cv);
cv = new ContentValues();
cv.put("stud_no", "S333");
cv.put("stud_name", "Peter Rabbit");
db.insert("Student", null, cv);
setTitle("insert record ok!");
```
查询语句：
```  
mOpenHelper = new DatabaseHelper(v.getContext());
SQLiteDatabase db = mOpenHelper.getReadableDatabase();
String col[] = { "stud_no", "stud_name" };
cur = db.query("Student", col, null, null, null, null, null);
Integer n = cur.getCount();
String ss = Integer.toString(n);
setTitle(ss + " records");
```
cur.moveToFirst();
public Cursor query(String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy)
selection：  A filter declaring which rows to return, formatted as an SQL WHERE clause (excluding the WHERE itself). Passing null will return all rows for the given table.
selectionArgs:You may include ?s in selection, which will be replaced by the values from selectionArgs, in order that they appear in the selection. The values will be bound as Strings.
如何不断循环的取出下一条打印出来：
查询语句返回的是一个Cursor cur = db.query("MyOrder", col, cond, null, null, null,null);
第一个参数为数据表的名字，第二个参数为一个String[],里边是数据列的名字，第三个是查询条件。翻译的SQL语句为：//SELECT order_no, type FROM MyOrder WHERE type='NEW'。
得到查询出来数据的个数。Integer n = cur.getCount();
如果想从头遍历的话：cur.moveToFirst();
如果想遍历完的话可以这么做：
```  
while (!cur.isAfterLast()) {
	String ss = cur.getString(0) + ", " + cur.getString(1);
	data[k++] = ss;
	cur.moveToNext();
}
public String getString(int columnIndex) {
}
```
对于当前条处理完了，想进入下一条目。moveToNext()；
```  
public String[] loadData() {
	SharedPreferences passwdfile = m_ctx.getSharedPreferences("CONDITION",
			0);
	String cond = passwdfile.getString("CONDITION", null);
	mOpenHelper = new DatabaseHelper(m_ctx);
	SQLiteDatabase db = mOpenHelper.getReadableDatabase();
	String col[] = { "order_no", "type" };
	Cursor cur = db.query("MyOrder", col, cond, null, null, null, null);
	// SELECT order_no, type FROM MyOrder WHERE type='NEW'
	Integer n = cur.getCount();
	String[] data = new String[n];
	cur.moveToFirst();
	int k = 0;
	while (!cur.isAfterLast()) {
		String ss = cur.getString(0) + ", " + cur.getString(1);
		data[k++] = ss;
		cur.moveToNext();
	}
	return data;
}
```
更新：
```  
// 更新条列
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
ContentValues cv = new ContentValues();
cv.put("stud_no", "S288");
cv.put("stud_name", "Linda Wang");
db.update("Student", cv, "stud_no = 'S201'", null);
```
删除：
```  
// 删除条列
mOpenHelper = new DatabaseHelper(v.getContext());
SQLiteDatabase db = mOpenHelper.getWritableDatabase();
db.delete("Student", "stud_no = 'S108'", null);
```