代码如下：
```  
// 删除最后一个字符
sb.deleteCharAt(sb.length() - 1);
SQLiteDatabase database = dbmanger.getWritableDatabase();
// execSQL是执行sql语句
database.execSQL("delete from person where personid in(" + sb + ")",
		(Object[]) ids);
// 分页查询 一
public List getdatePerson(int startResult, int maxResult) {
	List persons = new ArrayList();
	SQLiteDatabase database = dbmanger.getWritableDatabase();
	// Cursor是游标类 游标在数据库中其实就是一个数据集
	// rawQuery(String sql,String[]s) 参数一 是一个sql语句
	// 参数二是参数一sql语句中条件的占位符所存的具体值，这些值是一个字符string数组
	Cursor cursor = database.rawQuery(
			"select * from person limit ?,?",
			new String[] { String.valueOf(startResult),
					String.valueOf(maxResult) });
	while (cursor.moveToNext()) {
		persons.add(new Person(cursor.getInt(0), cursor.getString(1),
				cursor.getShort(2)));
	}
	return persons;
}
// 分页查询 二
public Cursor getdateRawPerson(int startResult, int maxResult) {
	// List persons=new ArrayList();
	SQLiteDatabase database = dbmanger.getWritableDatabase();
	// Cursor是游标类 游标在数据库中其实就是一个数据集
	// rawQuery(String sql,String[]s) 参数一 是一个sql语句
	// 参数二是参数一sql语句中条件的占位符所存的具体值，这些值是一个字符string数组
	return database.rawQuery(
			"select personid as _id,name,age from person limit ?,?",
			new String[] { String.valueOf(startResult),
					String.valueOf(maxResult) });
}
// 获取总记录数
public long getcount() {
	SQLiteDatabase database = dbmanger.getWritableDatabase();
	// Cursor是游标类 游标在数据库中其实就是一个数据集

	Cursor cursor = database.rawQuery("select count(*) from person", null);
	if (cursor.moveToLast()) {
		return cursor.getLong(0);
	}
	return 0;
}
```
3 业务类二
```  
import java.util.ArrayList;
import java.util.List;
import it.bean.Person;
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
[Tags]/**
 [Tags]* 此类不需要基于sql语句 进行增删查改操作 但是SQLiteDatabase对象是通过内部构造sql语句而执行操作的
 [Tags]*/
public class PersonSQLservice {
	private MangerDatabase dbmanger;
	public PersonSQLservice(Context context) {
		dbmanger = new MangerDatabase(context);
	}
	public void save(Person person) {
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		ContentValues values = new ContentValues();
		values.put("name", person.getName());
		values.put("age", person.getAge());
		// 参数 表名 构建insert语句的正确字段 字段映射
		database.insert("person", "name", values);
	}
	public void update(Person person) {
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		ContentValues values = new ContentValues();
		values.put("name", person.getName());
		values.put("age", person.getAge());
		// 参数 表名 更新映射关系 条件 占位符值
		database.update("person", values, "personid=?",
				new String[] { String.valueOf(person.getPersonId()) });
	}
	public Person find(Integer id) {
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		// 执行查询 参数 表名 返回的字段 指定条件 指定条件值 是否分组 分组条件 是否排序
		Cursor cursor = database.query("person", new String[] { "personid",
				"name", "age" }, "personid=?",
				new String[] { String.valueOf(id) }, null, null, null);
		if (cursor.moveToNext()) {
			return new Person(cursor.getInt(0), cursor.getString(1),
					cursor.getShort(2));
		}
		return null;
	}
}
```
