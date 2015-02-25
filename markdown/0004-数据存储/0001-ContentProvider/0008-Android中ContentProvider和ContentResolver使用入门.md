在Android中，我们的应用有的时候需要对外提供数据接口，可以有如下几种方法：1）AIDL 2）Broadcast 3）ContentProvider。
使用AIDL需要我们编写AIDL接口以及实现，而且对方也要有相应的接口描述，有点麻烦；使用Broadcast，我们不需要任何接口描述，只要协议文档就可以了，但是有点不好就是，这种方式不直接而且是异步的；使用ContentProvider我们不需要接口描述，只需要知道协议，同时这种方式是同步的，使用方便。下面是ContentProvider实现：
```  
import android.content.ContentProvider;
import android.content.ContentValues;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteQueryBuilder;
import android.net.Uri;
import android.util.Log;
public class TestContentProvider extends ContentProvider {
	private SQLiteDatabase mDb;
	private DatabaseHelper mDbHelper = null;
	private static final String DATABASE_NAME = "rssitems.db";
	private static final String DATABASE_TABLE_NAME = "rssItems";
	private static final int DB_VERSION = 1;
	private static final int ALL_MESSAGES = 1;
	private static final int SPECIFIC_MESSAGE = 2;
	// Set up our URL matchers to help us determine what an
	// incoming URI parameter is.
	private static final UriMatcher URI_MATCHER;
	static {
		URI_MATCHER = new UriMatcher(UriMatcher.NO_MATCH);
		URI_MATCHER.addURI("test", "item", ALL_MESSAGES);
		URI_MATCHER.addURI("test", "item/", SPECIFIC_MESSAGE);
	}
	// Here's the public URI used to query for RSS items.
	public static final Uri CONTENT_URI = Uri.parse("content://test/item");
	// Here are our column name constants, used to query for field values.
	public static final String ID = "_id";
	public static final String NAME = "NAME";
	public static final String VALUE = "VALUE";
	public static final String DEFAULT_SORT_ORDER = ID + " DESC";
	private static class DatabaseHelper extends AbstractDatabaseHelper {
		@Override
		protected String[] createDBTables() {
			String sql = "CREATE TABLE " + DATABASE_TABLE_NAME + "(" + ID
					+ " INTEGER PRIMARY KEY AUTOINCREMENT, " + NAME + " TEXT,
					+ VALUE + " TEXT);";
			return new String[] { sql };
		}
		@Override
		protected String[] dropDBTables() {
			return null;
		}
		@Override
		protected String getDatabaseName() {
			return DATABASE_NAME;
		}
		@Override
		protected int getDatabaseVersion() {
			return DB_VERSION;
		}
		@Override
		protected String getTag() {
			return TestContentProvider.class.getSimpleName();
		}
	}
	[Tags]/** */
	[Tags]/**
     [Tags]* 
     [Tags]*/
	public TestContentProvider() {
	}
	/*
	 [Tags]* @see android.content.ContentProviderdelete(android.net.Uri,
	 [Tags]* java.lang.String, java.lang.String[])
	 [Tags]*/
	@Override
	public int delete(Uri uri, String selection, String[] selectionArgs) {
		// NOTE Argument checking code omitted. Check your parameters!
		int rowCount = mDb
				.delete(DATABASE_TABLE_NAME, selection, selectionArgs);

		// Notify any listeners and return the deleted row count.
		getContext().getContentResolver().notifyChange(uri, null);
		return rowCount;
	}
	/*
	 [Tags]* @see android.content.ContentProvidergetType(android.net.Uri)
	 [Tags]*/
	@Override
	public String getType(Uri uri) {
		switch (URI_MATCHER.match(uri)) {
		case ALL_MESSAGES:
			return "vnd.android.cursor.dir/rssitem"; // List of items.
		case SPECIFIC_MESSAGE:
			return "vnd.android.cursor.item/rssitem"; // Specific item.
		default:
			return null;
		}
	}
	/*
	 [Tags]* @see android.content.ContentProviderinsert(android.net.Uri,
	 [Tags]* android.content.ContentValues)
	 [Tags]*/
	@Override
	public Uri insert(Uri uri, ContentValues values) {
		// NOTE Argument checking code omitted. Check your parameters! Check
		// that
		// your row addition request succeeded!
		long rowId = -1;
		rowId = mDb.insert(DATABASE_TABLE_NAME, NAME, values);
		Uri newUri = Uri.withAppendedPath(CONTENT_URI, "" + rowId);
		Log.i("TestContentProvider", "saved a record " + rowId + " " + newUri);
		// Notify any listeners and return the URI of the new row.
		getContext().getContentResolver().notifyChange(CONTENT_URI, null);
		return newUri;
	}
	/*
	 [Tags]* @see android.content.ContentProvideronCreate()
	 [Tags]*/
	@Override
	public boolean onCreate() {
		try {
			mDbHelper = new DatabaseHelper();
			mDbHelper.open(getContext());
			mDb = mDbHelper.getMDb();
		} catch (Exception e) {
			e.printStackTrace();
		}
		return true;
	}
	/*
	 [Tags]* @see android.content.ContentProviderquery(android.net.Uri,
	 [Tags]* java.lang.String[], java.lang.String, java.lang.String[],
	 [Tags]* java.lang.String)
	 [Tags]*/
	public Cursor query(Uri uri, String[] projection, String selection,
			String[] selectionArgs, String sortOrder) {
		// We won't bother checking the validity of params here, but you should!
		// SQLiteQueryBuilder is the helper class that creates the
		// proper SQL syntax for us.
		SQLiteQueryBuilder qBuilder = new SQLiteQueryBuilder();
		// Set the table we're querying.
		qBuilder.setTables(DATABASE_TABLE_NAME);
		// If the query ends in a specific record number, we're
		// being asked for a specific record, so set the
		// WHERE clause in our query.
		if ((URI_MATCHER.match(uri)) == SPECIFIC_MESSAGE) {
			qBuilder.appendWhere("_id=" + uri.getLastPathSegment());
			Log.i("TestContentProvider", "_id=" + uri.getLastPathSegment());
		}
		// Make the query.
		Cursor c = qBuilder.query(mDb, projection, selection, selectionArgs,
				null, null, sortOrder);
		Log.i("TestContentProvider", "get records");
		c.setNotificationUri(getContext().getContentResolver(), uri);
		return c;
	}
	/*
	 [Tags]* @see android.content.ContentProviderupdate(android.net.Uri,
	 [Tags]* android.content.ContentValues, java.lang.String, java.lang.String[])
	 [Tags]*/
	@Override
	public int update(Uri uri, ContentValues values, String selection,
			String[] selectionArgs) {
		// NOTE Argument checking code omitted. Check your parameters!
		int updateCount = mDb.update(DATABASE_TABLE_NAME, values, selection,
				selectionArgs);

		// Notify any listeners and return the updated row count.
		getContext().getContentResolver().notifyChange(uri, null);
		return updateCount;
	}
}
```
配置文件如下：
```  
<provider android:name="TestContentProvider"
	android:authorities="test">
</provider>
```
在客户端中可以使用如下方法进行调用：
```  
ContentValues values = new ContentValues();
values.put(TestContentProvider.NAME, "testname1");
values.put(TestContentProvider.VALUE, "testvalu1e");
Uri newAddUri = TestActivity.this.getContentResolver().insert(TestContentProvider.CONTENT_URI, values);
Cursor c = TestActivity.this.managedQuery(newAddUri, new String[]{TestContentProvider.NAME}, null, null, null);
Log.i("TestActivity", "" + c.getCount());
if(c.moveToNext())
{
	Log.i("TestActivity", c.getString(0));
}
```
上面的代码是先进行插入，然后进行查询并打印。就是如此简单，所有的应用如果需要都可以对外方便的提供数据接口，同时其他应用也可以很方便的进行调用。