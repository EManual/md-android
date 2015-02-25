我们这个是一个系列，所以我就不每一篇说怎么样了。我们直接上代码吧：
java代码：
```  
import java.util.List;
import it.bean.Person;
import it.service.PersonService;
import android.test.AndroidTestCase;
import android.util.Log;
[Tags]/**
 [Tags]* 
 [Tags]* 执行PersonService的测试
 [Tags]* 
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
5 测试类二
```  
import java.util.List;
import it.bean.Person;
import it.service.PersonSQLservice;
import android.test.AndroidTestCase;
import android.util.Log;
[Tags]/**
 [Tags]* 
 [Tags]* 执行PersonSQLservice 进行测试
 [Tags]* 
 [Tags]*/
public class PersonSQLserviceTest extends AndroidTestCase {
	private static final String TAG = "PersonSQLserviceTest";
	public void testsave() {
		PersonSQLservice psql = new PersonSQLservice(this.getContext());
		for (int i = 0; i < 10; i++) {
			psql.save(new Person("李渊", (short) 1));
		}
	}
	public void testFind() throws Exception {
		PersonSQLservice psql = new PersonSQLservice(this.getContext());
		Person person = psql.find(22);
		Log.i(TAG, person.toString());
	}
	public void testUpdate() throws Exception {
		PersonSQLservice psql = new PersonSQLservice(this.getContext());
		Person person = psql.find(1);
		person.setName("liming");
		psql.update(person);
		// Log.i(TAG, person.toString());
	}
	public void testGetCount() throws Exception {
		PersonSQLservice psql = new PersonSQLservice(this.getContext());
		Log.i(TAG, String.valueOf(psql.getCount()));
	}
	public void testGetScrollData() throws Exception {
		PersonSQLservice psql = new PersonSQLservice(this.getContext());
		List persons = psql.getScrollData(0, 20);
		for (Person person : persons) {
			Log.i(TAG, person.toString());
		}
	}
	public void testDelete() throws Exception {
		PersonSQLservice psql = new PersonSQLservice(this.getContext());
		psql.delete(1, 2, 3);
	}
}
```
