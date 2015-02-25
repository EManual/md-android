考虑到这一点，MediaStore内容提供器，这个用来分发图像，音频和视频数据的主内容提供器，利用了一个特殊的约定：用来获取关于这个二进制数据的元信息的query()或managedQuery()方法使用的URI，同样可以被openInputStream()方法用来数据本身。类似的，用来把元信息放进一个MediaStore记录里的insert()方法使用的URI，同样可以被openOutputStream()方法用来在那里存放二进制数据。下面的代码片断说明了这个约定：
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
批量更新记录Batch updating records
要批量更新一组记录（例如，把所有字段中的"NY"改为"New York"），可以传以需要改变的列和值参数来调用ContentResolver.update()方法。
删除一个记录Deleting a record
要删除单个记录，可以传以一个特定行的URI参数来调用ContentResolver.delete()方法。
要删除多行记录，可以传以需要被删除的记录类型的URI参数来调用ContentResolver.delete()方法（例如，android.provider.Contacts.People.CONTENT_URI）以及一个SQL WHERE 语句来定义哪些行要被删除。
创建一个内容提供器Creating a Content Provider
要创建一个内容提供器，你必须：
建立一个保存数据的系统。大多数内容提供器使用Android的文件储存方法或SQLite数据库来存放它们的数据，但是你可以用任何你想要的方式来存放数据。Android提供SQLiteOpenHelper类来帮助你创建一个数据库以及SQLiteDatabase类来管理它。
扩展ContentProvider类来提供数据访问接口。
在清单manifest文件中为你的应用程序声明这个内容提供器（AndroidManifest.xml）。
扩展ContentProvider类Extending the ContentProvider class
你可以定义一个ContentProvider子类来暴露你的数据给其它使用符合ContentResolver和游标Cursor对象约定的应用程序。理论上，这意味需要实现6个ContentProvider类的抽象方法：
```  
query() 
insert() 
update() 
delete() 
getType() 
onCreate()
```
query()方法必须返回一个游标Cursor对象可以用来遍历请求数据，游标本身是一个接口，但Android提供了一些现成的Cursor对象给你使用。例如，SQLiteCursor可以用来遍历SQLite数据库。你可以通过调用任意的SQLiteDatabase类的query()方法得到它。还有一些其它的游标实现-比如MatrixCursor-用来访问没有存放在数据库中的数据。
因为这些内容提供器的方法可以从不同的进程和线程的各个ContentResolver对象中调用，所以它们必须以线程安全的方式来实现。
周到起见，当数据被修改时，你可能还需要调用ContentResolver.notifyChange()方法来通知侦听者。
除了定义子类以外，你应该还需要采取其它一些步骤来简化客户端的工作和让这个类更容易被访问： 
定义一个public static final Uri 命名为CONTENT_URI。这是你的内容提供器处理的整个content:URI的字符串。你必须为它定义一个唯一的字符串。最佳方案是使用这个内容提供器的全称（fully qualified）类名（小写）。因此，例如，一个TransportationProvider类可以定义如下：
```  
public static final Uri CONTENT_URI = Uri
			.parse("content://com.example.codelab.transporationprovider");
```