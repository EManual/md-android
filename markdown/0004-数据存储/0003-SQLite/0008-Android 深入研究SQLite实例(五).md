我们现在就接着上一篇来讲述sqlite实例：
```  
public void delete(Integer... ids) {
	if (ids.length > 0) {
		StringBuilder sb = new StringBuilder();
		String[] strIds = new String[ids.length];
		for (int i = 0; i < ids.length; i++) {
			sb.append('?').append(',');
			strIds[i] = String.valueOf(ids[i]);
		}
		sb.deleteCharAt(sb.length() - 1);
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		// 参数 表名 指定条件 条件占位值
		database.delete("person", "personid in(" + sb + ")", strIds);
	}
}
public List getScrollData(int startResult, int maxResult) {
	List persons = new ArrayList();
	SQLiteDatabase database = dbmanger.getWritableDatabase();
	// 参数 表名 返回字段 指定条件 指定条件映射值 是否分组 分组条件 是否排序 分页条件
	Cursor cursor = database.query("person", new String[] { "personid",
			"name", "age" }, null, null, null, null, "personid desc",
			startResult + "," + maxResult);
	while (cursor.moveToNext()) {
		persons.add(new Person(cursor.getInt(0), cursor.getString(1),
				cursor.getShort(2)));
	}
	return persons;
}
public long getCount() {
	SQLiteDatabase database = dbmanger.getWritableDatabase();
	Cursor cursor = database.query("person", new String[] { "count(*)" },
			null, null, null, null, null);
	if (cursor.moveToNext()) {
		return cursor.getLong(0);
	}
	return 0;
}
import java.util.ArrayList;
import java.util.List;
import it.bean.Person;
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
 /**
  * 此类不需要基于sql语句 进行增删查改操作 但是SQLiteDatabase对象是通过内部构造sql语句而执行操作的
  * 
  */
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