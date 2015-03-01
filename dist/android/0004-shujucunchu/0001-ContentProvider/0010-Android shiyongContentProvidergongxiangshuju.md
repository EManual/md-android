当应用继承ContentProvider类，并重写该类用于提供数据和存储数据的方法，就可以向其他应用共享其数据。虽然使用其他方法也可以对外共享数据，但数据访问方式会因数据存储的方式而不同，如：采用文件方式对外共享数据，需要进行文件操作读写数据；采用sharedpreferences共享数据，需要使用sharedpreferences API读写数据。而使用ContentProvider共享数据的好处是统一了数据访问方式。
当应用需要通过ContentProvider对外共享数据时，第一步需要继承ContentProvider并重写下面方法：
```  
public class PersonContentProvider extends ContentProvider{
	public boolean onCreate()
	public Uri insert(Uri uri, ContentValues values)
	public int delete(Uri uri, String selection, String[] selectionArgs)
	public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)
	public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)
	public String getType(Uri uri)
}
```
第二步需要在AndroidManifest.xml使用<provider>对该ContentProvider进行配置，为了能让其他应用找到该ContentProvider ， ContentProvider 采用了authorities（主机名/域名）对它进行唯一标识，你可以把 ContentProvider看作是一个网站（想想，网站也是提供数据者），authorities 就是他的域名：
```  
<manifest>
<application android:icon="@drawable/icon" android:label="@string/app_name">
	<provider android:name=".PersonContentProvider" android:authorities="cn.android.provider.personprovider"/>
</application>
</manifest>
```
注意：一旦应用继承了ContentProvider类，后面我们就会把这个应用称为ContentProvider
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
	 /** */
	 /**
      * 
      */
	public TestContentProvider() {
	}
	/*
	  * @see android.content.ContentProviderdelete(android.net.Uri,
	  * java.lang.String, java.lang.String[])
	  */
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
	  * @see android.content.ContentProvidergetType(android.net.Uri)
	  */
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
	  * @see android.content.ContentProviderinsert(android.net.Uri,
	  * android.content.ContentValues)
	  */
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
	  * @see android.content.ContentProvideronCreate()
	  */
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
	  * @see android.content.ContentProviderquery(android.net.Uri,
	  * java.lang.String[], java.lang.String, java.lang.String[],
	  * java.lang.String)
	  */
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
	  * @see android.content.ContentProviderupdate(android.net.Uri,
	  * android.content.ContentValues, java.lang.String, java.lang.String[])
	  */
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
	
	<provider android:name="TestContentProvider
		android:authorities="test">
	</provider>
}
```
#### Uri介绍
Uri代表了要操作的数据，Uri主要包含了两部分信息：1》需要操作的ContentProvider，2》对ContentProvider中的什么数据进行操作，一个Uri由以下几部分组成：
ContentProvider（内容提供者）的scheme已经由Android所规定， scheme为：content://
主机名（或叫Authority）用于唯一标识这个ContentProvider，外部调用者可以根据这个标识来找到它。
路径（path）可以用来表示我们要操作的数据，路径的构建应根据业务而定，如下:
```  
要操作person表中id为10的记录，可以构建这样的路径:/person/10
要操作person表中id为10的记录的name字段， person/10/name
要操作person表中的所有记录，可以构建这样的路径:/person
要操作xxx表中的记录，可以构建这样的路径:/xxx
```
当然要操作的数据不一定来自数据库，也可以是文件等他存储方式，如下:要操作xml文件中person节点下的name节点，可以构建这样的路径：/person/name
如果要把一个字符串转换成Uri，可以使用Uri类中的parse()方法，如下：
```  
Uri uri = Uri.parse("content://cn.android.provider.personprovider/person")
```