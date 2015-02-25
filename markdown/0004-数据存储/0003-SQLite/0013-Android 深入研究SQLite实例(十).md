9 主应用 Activity
```  ：
import it.bean.Person;
import it.service.PersonService;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import android.app.Activity;
import android.database.Cursor;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.SimpleCursorAdapter;
import android.widget.Toast;
public class DataActivity extends Activity {
	private static final String TAG = "DataActivity";
	private ListView listview;
	private PersonService personservice;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 获取ListView
		listview = (ListView) this.findViewById(R.id.listview);
		// 获取数据库德数据
		personservice = new PersonService(this);
		[Tags]/**
		 [Tags]* 方法一 SimpleAdapter 适配器 绑定的参数数据是list对象 比较?嗦
		 [Tags]*/
		List persons = personservice.getdatePerson(9, 20);
		// 绑定数据 设置适配器
		List list = new ArrayList();
		[Tags]/**
		 [Tags]* 适配器有 ArrayAdapter T 可以是String Integer
		 [Tags]* SimpleAdapter,SimpleCursorAdapter
		 [Tags]*/
		HashMap hs = new HashMap();
		hs.put("personid", "编号");
		hs.put("name", "名称");
		hs.put("age", "年龄");
		list.add(hs);
		for (Person person : persons) {
			HashMap map = new HashMap();
			map.put("personid", String.valueOf(person.getPersonId()));
			map.put("name", person.getName());
			map.put("age", String.valueOf(person.getAge()));
			list.add(map);
		}
		[Tags]/**
		 [Tags]* 定义一个适配器 参数一 上下文信息 当前的上下文信息 是当前的类 参数二 加载的值 参数三 加载的视图界面文件 参数四 加载的目录
		 [Tags]* 这个目录 是根据键值去取的值 在上面已经设置好了这个键值对 参数五 加载的数据对应的属性
		 [Tags]*/
		SimpleAdapter adapter = new SimpleAdapter(DataActivity.this, list,
				R.layout.person, new String[] { "personid", "name", "age" },
				new int[] { R.id.personid, R.id.name, R.id.age });
		// 给这个ListView设置初始的适配器
		listview.setAdapter(adapter);
		// 为ListView添加事件
		listview.setOnItemClickListener(new OnItemClickListener() {
			[Tags]/**
			 [Tags]* 参数一 表示 点击的 listview 参数二 表示点击的最外层的那个元素 说明 int position, long id
			 [Tags]* 是所在行的id
			 [Tags]*/
			@Override
			public void onItemClick(AdapterView parent, View view,
					int position, long id) {
				ListView listView = (ListView) parent;
				// 获取所在行的数据 position和id都表示选择的item数据
			}
		});
	}
}
```
