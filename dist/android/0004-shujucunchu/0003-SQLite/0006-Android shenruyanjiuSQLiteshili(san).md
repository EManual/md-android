java代码：
```  
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
java代码：
```  
import java.util.ArrayList;
import java.util.List;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.util.Log;
import it.bean.Person;
 /**
  * 
  * 实体操作类 rawQuery 执行sql查询 execSQL 执行增删 改的sql 由SQLiteOpenHelper 的继承类
  * MangerDatabase获取数据库管理实例 由SQLiteDatabase的对象去获取这个管理实例
  * 这个对象可以执行rawQuery和execSQL方法
  */
public class PersonService {
	private MangerDatabase dbmanger;
	public PersonService(Context context) {
		dbmanger = new MangerDatabase(context);
	}
	// 保存
	public void save(Person person) {
		 /**
		  * 打开数据库 取得数据操作对象 getWritableDatabase()和getReadableDatabase()
		  * 方法都可以获取一个用于操作数据库的SQLiteDatabase实例。 但getWritableDatabase()
		  * 方法以读写方式打开数据库，一旦数据库的磁盘空间满了，数据库就只能读而不能写， 倘若使用的是getWritableDatabase()
		  * 方法就会出错。 getReadableDatabase()方法先以读写方式打开数据库，如果数据库的磁盘空间满了，就会打开失败
		  * 当打开失败后会继续尝试以只读方式打开数据库。 SQLiteDatabase sqlite数据库的管理类
		  */
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		database.execSQL("insert into person(name,age) values(?,?)",
				new Object[] { person.getName(), person.getAge() });
	}
	// 更新
	public void update(Person person) {
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		// execSQL是执行sql语句
		database.execSQL(
				"update person set name=?,age=? where personid=?",
				new Object[] { person.getName(), person.getAge(),
						person.getPersonId() });
	}
	// 根据id执行查询数据
	public Person findbyid(Integer id) {
		SQLiteDatabase database = dbmanger.getWritableDatabase();
		// Cursor是游标类 游标在数据库中其实就是一个数据集
		Cursor cursor = database.rawQuery(
				"select * from person where personid=?",
				new String[] { String.valueOf(id) });
		if (cursor.moveToNext()) {
			Log.i("xxx", "xxx" + String.valueOf(cursor.getInt(3)));
			Person person = new Person(cursor.getInt(0), cursor.getString(1),
					cursor.getShort(2));
			return person;
		}
		return null;
	}
	// 删除
	public void delete(Integer... ids) {
		if (ids.length > 0) {
			StringBuilder sb = new StringBuilder();
			for (Integer id : ids) {
				sb.append('?').append(',');

			}
		}
	}
}
```
