代码如下：
```  
import java.util.List;
import it.bean.Person;
import it.service.PersonSQLservice;
import android.test.AndroidTestCase;
import android.util.Log;
[Tags]/**
 [Tags]* 执行PersonSQLservice 进行测试
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
在做测试的时候 必须要对其应用进行配置
```  
<xmlns:android="http://schemas.android.com/apk/res/android"> 
package="ac.demo"
android:versionCode="1"
android:versionName="1.0">
<android:name=".DataActivity"> 
android:label="@string/app_name">
<android:name="android.test.InstrumentationTestRunner"> 
android:targetPackage="it.date" 
android:label="Tests for My App" />
```
6 视图界面文件一
```  
<xmlns:android="http://schemas.android.com/apk/res/android"
	<TextView
		android:layout_width="fill_parent"
		android:layout_height="fill_parent">
		android:layout_width="40px"
		android:layout_height="wrap_content"
		android:textSize="20px"
		android:id="@+id/personid"
		/>
	<TextView
		android:layout_width="150px"
		android:layout_height="wrap_content"
		android:layout_toRightOf="@id/personid"
		android:layout_alignTop="@id/personid"
		android:gravity="center_horizontal"
		android:textSize="20px"
		android:id="@+id/name"
		/>
	<TextView
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:layout_alignTop="@id/name"
		android:layout_toRightOf="@id/name"
		android:gravity="right"
		android:textSize="20px"
		android:id="@+id/age"
		/>
```