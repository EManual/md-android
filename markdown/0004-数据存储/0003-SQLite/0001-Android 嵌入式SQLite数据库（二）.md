#### CRUD操作
1.和JDBC访问数据库不同，操作SQLite数据库无需加载驱动，不用获取连接，直接可以使用。
获取SQLiteDatabase对象之后通过该对象直接可以执行SQL语句。
```  
SQLiteDatabase.execSQL()
SQLiteDatabase.rawQuery()
```
2.getReadableDatabase()和getWritableDatabase()的区别
查看源代码后我们发现getReadableDatabase()在通常情况下返回的就是getWritableDatabase()拿到的数据库。
只有在抛出异常的时候才会以只读方式打开。
3.数据库对象缓存
getWritableDatabase()方法最后会使用一个成员变量记住这个数据库对象，下次打开时判断是否重用。
4.SQLiteDatabase封装了insert()、delete()、update()、query()四个方法也可以对数据库进行操作。
这些方法封装了部分SQL语句，通过参数进行拼接。
执行crud操作有两种方式，第一种方式自己写sql语句执行操作，第二种方式是使用SQLiteDatabase类调用响应的方法执行操作。
execSQL()方法可以执行insert、delete、update和CREATETABLE之类有更改行为的SQL语句； rawQuery()方法用于执行select语句。
```  
import java.util.ArrayList;
import java.util.List;
import Android.content.Context;
import Android.database.Cursor;
import Android.database.sqlite.SQLiteDatabase;
import cn.itcast.sqlite.DBOpenHelper;
import cn.itcast.sqlite.domain.Person;
public class SQLPersonService {
	private DBOpenHelper helper;
	public SQLPersonService(Context context) {
		helper = new DBOpenHelper(context, "itcast.db", null, 2);// 初始化数据库
	}
	[Tags]/**
	 [Tags]* 插入一个Person
	 [Tags]* @param p
	 [Tags]* 要插入的Person
	 [Tags]*/
	public void insert(Person p) {
		SQLiteDatabase db = helper.getWritableDatabase(); // 获取到数据库
		db.execSQL("INSERT INTO person(name,phone,balance) VALUES(?,?)",
				new Object[] { p.getName(), p.getPhone() });
		db.close();
	}
	[Tags]/**
	 [Tags]* 根据ID删除
	 [Tags]* @param id
	 [Tags]* 要删除的PERSON的ID
	 [Tags]*/
	public void delete(Integer id) {
		SQLiteDatabase db = helper.getWritableDatabase();
		db.execSQL("DELETE FROM person WHERE id=?", new Object[] { id });
		db.close();
	}
	[Tags]/**
	 [Tags]* 更新Person
	 [Tags]* @param p
	 [Tags]* 要更新的Person
	 [Tags]*/
	public void update(Person p) {
		SQLiteDatabase db = helper.getWritableDatabase();
		db.execSQL(
				"UPDATE person SET name=?,phone=?,balance=? WHERE id=?",
				new Object[] { p.getName(), p.getPhone(), p.getBalance(),
						p.getId() });
		db.close();
	}
	[Tags]/**
	 [Tags]* 根据ID查找
	 [Tags]* @param id 要查的ID
	 [Tags]* @return 对应的对象, 如果未找到返回null
	 [Tags]*/
	public Person find(Integer id) {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.rawQuery(
				"SELECT name,phone,balance FROM person WHERE id=?",
				new String[] { id.toString() });
		Person p = null;
		if (cursor.moveToNext()) {
			String name = cursor.getString(cursor.getColumnIndex("name"));
			String phone = cursor.getString(1);
			Integer balance = cursor.getInt(2);
			p = new Person(id, name, phone, balance);
		}
		cursor.close();
		db.close();
		return p;
	}
	[Tags]/**
	 [Tags]* 查询所有Person对象
	 [Tags]* @return Person对象集合, 如未找到, 返回一个size()为0的List
	 [Tags]*/
	public List<Person> findAll() {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.rawQuery("SELECT id,name,phone,balance FROM person",
				null);
		List<Person> persons = new ArrayList<Person>();
		while (cursor.moveToNext()) {
			Integer id = cursor.getInt(0);
			String name = cursor.getString(1);
			String phone = cursor.getString(2);
			Integer balance = cursor.getInt(3);
			persons.add(new Person(id, name, phone, balance));
		}
		cursor.close();
		db.close();
		return persons;
	}
	[Tags]/**
	 [Tags]* 查询某一页数据
	 [Tags]* @param page 页码
	 [Tags]* @param size 每页记录数
	 [Tags]*/
	public List<Person> findPage(int page, int size) {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.rawQuery(
				"SELECT id,name,phone,balance FROM person LIMIT ?,?",
				new String[] { String.valueOf((page - 1) * size),
						String.valueOf(size) });
		List<Person> persons = new ArrayList<Person>();
		while (cursor.moveToNext()) {
			Integer id = cursor.getInt(0);
			String name = cursor.getString(1);
			String phone = cursor.getString(2);
			Integer balance = cursor.getInt(3);
			persons.add(new Person(id, name, phone, balance));
		}
		cursor.close();
		db.close();
		return persons;
	}
	[Tags]/**
	 [Tags]* 获取记录数
	 [Tags]* @return 记录数
	 [Tags]*/
	public int getCount() {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.rawQuery("SELECT COUNT(*) FROM person", null);
		cursor.moveToNext();
		return cursor.getInt(0);
	}
}
```