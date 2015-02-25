第二种方式示例：
```  
public class Snippet {
	[Tags]/**
	 [Tags]* 插入一个Person
	 [Tags]* @param p 要插入的Person
	 [Tags]*/
	public void insert(Person p) { 
		SQLiteDatabase db = helper.getWritableDatabase(); 7.
		ContentValues values = new ContentValues();
		values.put("name", p.getName());
		values.put("phone", p.getPhone());
		values.put("balance", p.getBalance());
		// 第一个参数是表名, 第二个参数是如果要插入一条空记录时指定的某一列的名字, 第三个参数是数据 
		db.insert("person", null, values);
		db.close();
	}
	[Tags]/**
	 [Tags]* 根据ID删除
	 [Tags]* @param id 要删除的PERSON的ID
	 [Tags]*/
	public void delete(Integer id) {
		SQLiteDatabase db = helper.getWritableDatabase();
		db.delete("person", "id=?", new String[] { id.toString() });
		db.close();
	}
	[Tags]/**
	 [Tags]* 更新Person
	 [Tags]* @param p 要更新的Person
	 [Tags]*/
	public void update(Person p) {
		SQLiteDatabase db = helper.getWritableDatabase();
		ContentValues values = new ContentValues();
		values.put("id", p.getId());
		values.put("name", p.getName());
		values.put("phone", p.getPhone());
		values.put("balance", p.getBalance());
		db.update("person", values, "id=?",
				new String[] { p.getId().toString() });
		db.close();
	}
	[Tags]/**
	 [Tags]* 根据ID查找
	 [Tags]* @param id 要查的ID
	 [Tags]* @return 对应的对象, 如果未找到返回null
	 [Tags]*/
	public Person find(Integer id) {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.query("person", new String[] { "name", "phone",
				"balance" }, "id=?", new String[] { id.toString() }, null,
				null, null);
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
		Cursor cursor = db.query("person", new String[] { "id", "name",
				"phone", "balance" }, null, null, null, null, "id desc");
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
	 [Tags]* @return
	 [Tags]*/
	public List<Person> findPage(int page, int size) {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.query("person", new String[] { "id", "name",
				"phone", "balance" }, null, null, null, null, null, (page - 1)
				[Tags]* size + "," + size);
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
		Cursor cursor = db.query("person", new String[] { "COUNT(*)" }, null,
				null, null, null, null);
		cursor.moveToNext();
		return cursor.getInt(0);
	}
}
```