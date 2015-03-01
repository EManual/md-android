我给大家介绍把图片保存到数据库的方法。
方法一：
```  
public void saveIcon(Bitmap icon) {
	if (icon == null) {
		return;
	}
	// 最终图标要保存到浏览器的内部数据库中，系统程序均保存为SQLite格式，Browser也不例外，因为图片是二进制的所以使用字节数组存储数据库的
	// BLOB类型
	final ByteArrayOutputStream os = new ByteArrayOutputStream();
	// 将Bitmap压缩成PNG编码，质量为100%存储
	icon.compress(Bitmap.CompressFormat.PNG, 100, os);
	// 构造SQLite的Content对象，这里也可以使用raw
	ContentValues values = new ContentValues();
	// 写入数据库的Browser.BookmarkColumns.TOUCH_ICON字段
	values.put(Browser.BookmarkColumns.TOUCH_ICON, os.toByteArray());
	DBUtil.update(....);//调用更新或者插入到数据库的方法
}
```
方法二：如果数据表入口时一个content:URI
```  
// Save the name and description of an image in a ContentValues map.
ContentValues values = new ContentValues(3);
values.put(Media.DISPLAY_NAME, "road_trip_1");
values.put(Media.DESCRIPTION, "Day 1, trip to Los Angeles");
values.put(Media.MIME_TYPE, "image/jpeg");
// Add a new record without the bitmap, but with the values just set.
// insert() returns the URI of the new record.
Uri uri = getContentResolver().insert(Media.EXTERNAL_CONTENT_URI,
		values);
// Now get a handle to the file for that record, and save the data into
// it.
// Here, sourceBitmap is a Bitmap object representing the file to save
// to the database.
try {
	OutputStream outStream = getContentResolver().openOutputStream(uri);
	sourceBitmap.compress(Bitmap.CompressFormat.JPEG, 50, outStream);
	outStream.close();
} catch (Exception e) {
	Log.e(TAG, "exception while writing image", e);
}
```
