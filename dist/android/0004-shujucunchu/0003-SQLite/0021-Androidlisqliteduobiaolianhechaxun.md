对于Android平台上的数据库而言使用了嵌入式越来越流行的SQLite，为了更好的跨平台eoe推荐大家使用原始SQL语句直接操作，在代码和处理效率上都有不小的提高，不过要做好SQL语句异常处理。
下面我们说下rawQuery的好处，可以看到查询的代码直接使用SQL语句，通过性能实测效率比Android封装过的类要快不少，但不能配合一些Adapter的使用，不过总体上在跨平台上很突出，下面为本地使用方法的伪代码，没有做任何构造和实例化，希望让大家知道rawSQL的优势在 Android平台上的使用。
```  
SQLiteDatabase db;
String args[] = { id };
ContentValues cv = new ContentValues();
cv.put("android123", id);
Cursor c = db.rawQuery("SELECT * FROM table WHERE android123=?", args); // 执行本地SQL语句查询
if (c.getCount() != 0) {
	// dosomething
	ContentValues cv = new ContentValues();
	cv.put("android123", "cwj");
	db.insert("table", "android123", cv); // 插入数据
	String args[] = { id };
	ContentValues cv2 = new ContentValues();
	cv2.put("android123", id);
	db.delete("table", "android123=?", args); // 删除数据
}
```