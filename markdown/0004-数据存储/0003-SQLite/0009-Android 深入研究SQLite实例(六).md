java代码：
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
```
4 测试类一
```  
import java.util.List;
import it.bean.Person;
import it.service.PersonService;
import android.test.AndroidTestCase;
import android.util.Log;
[Tags]/**
 [Tags]* 执行PersonService的测试
 [Tags]*/
public class PersonServiceTest extends AndroidTestCase {
	private static final String tag = "PersonServiceTest";
	public void testsave() throws Exception {
		PersonService personservice = new PersonService(this.getContext());
		for (int i = 0; i < 10; i++) {
			personservice.save(new Person("huhuanhuan", (short) 33));
		}
	}
	public void testfindbyid() {
		PersonService personservice = new PersonService(this.getContext());
		Person p = personservice.findbyid(1);
		Log.i(tag, p.toString());
	}
	public void testupdate() {
		Person person = new Person(1, "chun", (short) 20);
		PersonService personservice = new PersonService(this.getContext());
		personservice.update(person);
		Person p = personservice.findbyid(1);
		Log.i(tag, p.toString());
	}
	public void testgetdatePerson() {
		Person p = new Person();
		PersonService personservice = new PersonService(this.getContext());
		List list = personservice.getdatePerson(0, 10);
		for (int i = 0; i < list.size(); i++) {
			p = (Person) list.get(i);
			Log.i(tag, p.toString());
		}
	}
	public void testgetcount() {
		PersonService personservice = new PersonService(this.getContext());
		long l = personservice.getcount();
		Log.i(tag, String.valueOf(l));
	}
	public void testdelete() {
		PersonService personservice = new PersonService(this.getContext());
		personservice.delete(1, 2, 3);
	}
}
```
