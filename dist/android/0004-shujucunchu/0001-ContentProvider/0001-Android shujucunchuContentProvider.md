一、ContentProvider简介
当应用继承ContentProvider类，并重写该类用于提供数据和存储数据的方法，就可以向其他应用共享其数据。虽然使用其他方法也可以对外共享数据，但数据访问方式会因数据存储的方式而不同，如：采用文件方式对外共享数据，需要进行文件操作读写数据；采用sharedpreferences共享数据，需要使用sharedpreferences API读写数据。而使用ContentProvider共享数据的好处是统一了数据访问方式。
二、Uri类简介
Uri代表了要操作的数据，Uri主要包含了两部分信息：1.需要操作的ContentProvider，2.对ContentProvider中的什么数据进行操作，一个Uri由以下几部分组成：
1.scheme：ContentProvider（内容提供者）的scheme已经由Android所规定为：content://。
2.主机名（或Authority）：用于唯一标识这个ContentProvider，外部调用者可以根据这个标识来找到它。
3.路径（path）：可以用来表示我们要操作的数据，路径的构建应根据业务而定，如下：
· 要操作contact表中id为10的记录，可以构建这样的路径:/contact/10
· 要操作contact表中id为10的记录的name字段， contact/10/name
· 要操作contact表中的所有记录，可以构建这样的路径:/contact
要操作的数据不一定来自数据库，也可以是文件等他存储方式，如下:
要操作xml文件中contact节点下的name节点，可以构建这样的路径：/contact/name
如果要把一个字符串转换成Uri，可以使用Uri类中的parse()方法，如下：
```  
Uri uri = Uri.parse("content://com.changcheng.provider.contactprovider/contact")
```
ContentProvider示例程序：
```  
import com.changcheng.sqlite.MyOpenHelper;
import android.content.ContentProvider;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.net.Uri;
public class ContactContentProvider extends ContentProvider {
	// 通过UriMatcher匹配外部请求
	private static UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
	// 通过openHelper进行数据库读写
	private MyOpenHelper openHelper;
	// 匹配状态常量
	private static final int CONTACT_LIST = 1;
	private static final int CONTACT = 2;
	// 表名
	private static final String tableName = "contacts";
	// 添加Uri
	static {
		uriMatcher.addURI("com.changcheng.sqlite.provider", "contact",
				CONTACT_LIST);
		uriMatcher.addURI("com.changcheng.sqlite.provider", "contact/",
				CONTACT);
	}
	@Override
	public int delete(Uri uri, String selection, String[] selectionArgs) {
		SQLiteDatabase db = this.openHelper.getWritableDatabase();
		int result;
		switch (uriMatcher.match(uri)) {
		case CONTACT_LIST:
			result = db.delete(tableName, selection, selectionArgs);
			break;
		case CONTACT:
			long id = ContentUris.parseId(uri);
			String where = "_id=" + id;
			if (selection != null && !"".equals(selection)) {
				where = where + " and " + selection;
			}
			result = db.delete(tableName, where, selectionArgs);
			break;
		default:
			throw new IllegalArgumentException("Uri IllegalArgument:" + uri);
		}
		return result;
	}
	@Override
	public String getType(Uri uri) {
		switch (uriMatcher.match(uri)) {
		case CONTACT_LIST:// 集合类型必须在前面加上vnd.android.cursor.dir/
			return "vnd.android.cursor.dir/contactlist";
		case CONTACT:// 非集合类型必须在前面加上vnd.android.cursor.item/
			return "vnd.android.cursor.item/contact";
		default:
			throw new IllegalArgumentException("Uri IllegalArgument:" + uri);
		}
	}
	@Override
	public Uri insert(Uri uri, ContentValues values) {
		SQLiteDatabase db = this.openHelper.getWritableDatabase();
		long id;
		switch (uriMatcher.match(uri)) {
		case CONTACT_LIST:
			// 因为后台需要生成SQL语句，当values为null时，必须提第二个参数。生成的SQL语句才不会出错！
			id = db.insert(tableName, "_id", values);
			return ContentUris.withAppendedId(uri, id);
		case CONTACT:
			id = db.insert(tableName, "_id", values);

			String uriPath = uri.toString();
			String path = uriPath.substring(0, uriPath.lastIndexOf("/")) + id;
			return Uri.parse(path);
		default:
			throw new IllegalArgumentException("Uri IllegalArgument:" + uri);
		}
	}
	@Override
	public boolean onCreate() {
		this.openHelper = new MyOpenHelper(this.getContext());
		return true;
	}
	@Override
	public Cursor query(Uri uri, String[] projection, String selection,
			String[] selectionArgs, String sortOrder) {
		SQLiteDatabase db = this.openHelper.getWritableDatabase();
		switch (uriMatcher.match(uri)) {
		case CONTACT_LIST:
			return db.query(tableName, projection, selection, selectionArgs,
					null, null, sortOrder);
		case CONTACT:
			long id = ContentUris.parseId(uri);
			String where = "_id=" + id;
			if (selection != null && !"".equals(selection)) {
				where = where + " and " + selection;
			}
			return db.query(tableName, projection, where, selectionArgs, null,
					null, sortOrder);
		default:
			throw new IllegalArgumentException("Uri IllegalArgument:" + uri);
		}
	}
	@Override
	public int update(Uri uri, ContentValues values, String selection,
			String[] selectionArgs) {
		SQLiteDatabase db = this.openHelper.getWritableDatabase();
		int result;
		switch (uriMatcher.match(uri)) {
		case CONTACT_LIST:
			result = db.update(selection, values, selection, selectionArgs);
			break;
		case CONTACT:
			long id = ContentUris.parseId(uri);
			String where = "_id=" + id;
			if (selection != null && !"".equals(selection)) {
				where = where + " and " + selection;
			}
			result = db.update(tableName, values, where, selectionArgs);
			break;
		default:
			throw new IllegalArgumentException("Uri IllegalArgument:" + uri);
		}
		return result;
	}	
}
```
添加ContentProvider配置
```  
<provider android:name=".provider.ContactContentProvider" android:authorities="eoe.demo.sqlite.provider.contactprovider"/>
```
测试SQLite示例程序的ContentProvider
```  
public void testQuery() throws Throwable {
	ContentResolver contentResolver = this.getContext()
			.getContentResolver();
	Uri uri = Uri.parse("content://com.changcheng.sqlite.provider/contact");
	Cursor cursor = contentResolver.query(uri, new String[] { "_id",
			"name", "phone" }, null, null, "_id desc");
	while (cursor.moveToNext()) {
		Log.i(TAG,
				"_id=" + cursor.getInt(0) + ",name=" + cursor.getString(1)
						+ ",phone=" + cursor.getString(2));
	}
}
```
