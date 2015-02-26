第二种方式示例：
```  
public class Snippet {
	 /**
	  * 插入一个Person
	  * @param p 要插入的Person
	  */
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
	 /**
	  * 根据ID删除
	  * @param id 要删除的PERSON的ID
	  */
	public void delete(Integer id) {
		SQLiteDatabase db = helper.getWritableDatabase();
		db.delete("person", "id=?", new String[] { id.toString() });
		db.close();
	}
	 /**
	  * 更新Person
	  * @param p 要更新的Person
	  */
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
	 /**
	  * 根据ID查找
	  * @param id 要查的ID
	  * @return 对应的对象, 如果未找到返回null
	  */
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
	 /**
	  * 查询所有Person对象
	  * @return Person对象集合, 如未找到, 返回一个size()为0的List
	  */
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
	 /**
	  * 查询某一页数据
	  * @param page 页码
	  * @param size 每页记录数
	  * @return
	  */
	public List<Person> findPage(int page, int size) {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.query("person", new String[] { "id", "name",
				"phone", "balance" }, null, null, null, null, null, (page - 1)
				 * size + "," + size);
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
	 /**
	  * 获取记录数
	  * @return 记录数
	  */
	public int getCount() {
		SQLiteDatabase db = helper.getReadableDatabase();
		Cursor cursor = db.query("person", new String[] { "COUNT(*)" }, null,
				null, null, null, null);
		cursor.moveToNext();
		return cursor.getInt(0);
	}
}
```