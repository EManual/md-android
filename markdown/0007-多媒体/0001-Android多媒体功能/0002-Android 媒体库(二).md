MediaContentProvider.java
```  
import java.util.HashMap;
import android.content.ContentProvider;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.Context;
import android.content.UriMatcher;
import android.database.Cursor;
import android.database.SQLException;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.database.sqlite.SQLiteQueryBuilder;
import android.net.Uri;
import android.text.TextUtils;
import android.util.Log;
import java.util.Random;
public class MediaContentProvider extends ContentProvider {
	private final static String TAG = MediaContentProvider.class.getSimpleName();
	private final static String DATABASE_NAME = "mGrevianDB.db";
	private final static String DATABASE_TABLE = "t_media";
	private final static int DATABASE_VERSION = 2;
	private static final int MEDIA = 1;
	private static final int MEDIA_UPC = 2;
	private static final int MEDIA_SEARCH = 3;
	private static final UriMatcher sUriMatcher;
	private static HashMap<String, String> sMediaProjectionMap;
	SQLDataSource mDataSource;
	public static final Uri CONTENT_URI = Uri.parse("content://grevian.MediaLibrary.MediaContentProvider");
	private class SQLDataSource extends SQLiteOpenHelper {
		SQLDataSource(Context context) {
			super(context, DATABASE_NAME, null, DATABASE_VERSION);
			// onUpgrade(this.getWritableDatabase(), 1, 2);
			// insertRandomRows(30);
		}
		@SuppressWarnings("unused")
		private void insertRandomRows(int entries) {
			// Initialize required objects
			ContentValues values = new ContentValues();
			String chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
			StringBuilder titleBuilder = new StringBuilder();
			Random mRand = new Random();
			for (int i = 0; i < entries; i++) {
				// Clear existing objects (Object creation is expensive, so
				// avoid recreating them)
				values.clear();
				titleBuilder.delete(0, titleBuilder.length());
				// Stick a not really real barcode into the values
				values.put("barcode", 10000 + i);
				// about half and half for owned vs not owned
				if (Math.random() < 0.5)
					values.put("owned", 1);
				else
					values.put("owned", 0);
				// Loaned and Summary will always be empty for now,
				values.put("loaned", "");
				values.put("summary", "");
				// generate a random 20 character title from the allowable
				// characters
				for (int p = 0; p < 20; p++) {
					titleBuilder.append(chars.charAt(mRand.nextInt(chars.length())));
				}
				values.put("title", titleBuilder.toString());
				// Insert into the database
				this.getWritableDatabase().insert(DATABASE_TABLE, null, values);
			}
		}
		@Override
		public void onCreate(SQLiteDatabase db) {
			db.execSQL(""
					+ "create table
					+ DATABASE_TABLE
					+ "( barcode integer primary key, title varchar, owned int(2), loaned varchar, summary varchar );
					+ "");
		}
		@Override
		public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
			Log.w(TAG, "Upgrading database from version " + oldVersion + " to
					+ newVersion + ", which will destroy all old data");
			db.execSQL("DROP TABLE IF EXISTS " + DATABASE_TABLE);
			onCreate(db);
		}
	}
	@Override
	public boolean onCreate() {
		mDataSource = new SQLDataSource(getContext());
		return true;
	}
	@Override
	public Uri insert(Uri uri, ContentValues initialValues) {
		// Validate the requested uri
		if (sUriMatcher.match(uri) != MEDIA) {
			throw new IllegalArgumentException("Unknown URI " + uri);
		}
		SQLiteDatabase db = mDataSource.getWritableDatabase();
		long rowId = db.insert(DATABASE_TABLE, "", initialValues);
		if (rowId > 0) {
			Uri noteUri = ContentUris.withAppendedId(Media.CONTENT_URI, rowId);
			getContext().getContentResolver().notifyChange(noteUri, null);
			return noteUri;
		}
		throw new SQLException("Failed to insert row into " + uri);
	}
	@Override
	public int delete(Uri uri, String where, String[] whereArgs) {
		SQLiteDatabase db = mDataSource.getWritableDatabase();
		int count;
		switch (sUriMatcher.match(uri)) {
		case MEDIA:
			count = db.delete(DATABASE_TABLE, where, whereArgs);
			break;
		case MEDIA_UPC:
			String noteId = uri.getPathSegments().get(1);
			count = db.delete(DATABASE_TABLE,
					Media.BARCODE
							+ "=
							+ noteId
							+ (!TextUtils.isEmpty(where) ? " AND (" + where
									+ ')' : ""), whereArgs);
			break;
		case MEDIA_SEARCH:
			throw new IllegalArgumentException("Cannot Delete wildcards.");
		default:
			throw new IllegalArgumentException("Unknown URI " + uri);
		}
		getContext().getContentResolver().notifyChange(uri, null);
		return count;
	}
	@Override
	public String getType(Uri uri) {
		switch (sUriMatcher.match(uri)) {
		case MEDIA:
			return Media.CONTENT_TYPE;
		case MEDIA_UPC:
			return Media.CONTENT_ITEM_TYPE;
		case MEDIA_SEARCH:
			return Media.CONTENT_TYPE;
		default:
			throw new IllegalArgumentException("Unknown URI " + uri);
		}
	}
	@Override
	public Cursor query(Uri uri, String[] projection, String selection,
			String[] selectionArgs, String sortOrder) {
		SQLiteQueryBuilder qb = new SQLiteQueryBuilder();
		Log.i(TAG, "Executing Query in MediaContentProvider for uri: " + uri.toString());
		qb.setTables(DATABASE_TABLE);
		qb.setProjectionMap(sMediaProjectionMap);
		switch (sUriMatcher.match(uri)) {
		case MEDIA:
			break;
		case MEDIA_UPC:
			qb.appendWhere(Media.BARCODE + "=" + uri.getPathSegments().get(1));
			break;
		case MEDIA_SEARCH:
			// FIXME: Probably injectable...
			qb.appendWhere(Media.TITLE + " like '%" + uri.getPathSegments().get(1) + "%'");
			break;
		default:
			throw new IllegalArgumentException("Unknown URI " + uri);
		}
		// If no sort order is specified use the default, otherwise use whatever
		// was passed in
		if (TextUtils.isEmpty(sortOrder)) {
			sortOrder = Media.DEFAULT_SORT_ORDER;
		}
		// Get the database and run the query
		SQLiteDatabase db = mDataSource.getReadableDatabase();
		Cursor c = qb.query(db, projection, selection, selectionArgs, null, null, sortOrder);
		// Tell the cursor what uri to watch, so it knows when its source data
		// changes
		c.setNotificationUri(getContext().getContentResolver(), uri);
		return c;
	}
	@Override
	public int update(Uri uri, ContentValues values, String where,
			String[] whereArgs) {
		SQLiteDatabase db = mDataSource.getWritableDatabase();
		int count;
		switch (sUriMatcher.match(uri)) {
		case MEDIA:
			count = db.update(DATABASE_TABLE, values, where, whereArgs);
			break;

		case MEDIA_UPC:
			String noteId = uri.getPathSegments().get(1);
			count = db.update(DATABASE_TABLE, values,
					Media.BARCODE
							+ "=
							+ noteId
							+ (!TextUtils.isEmpty(where) ? " AND (" + where
									+ ')' : ""), whereArgs);
			break;
		case MEDIA_SEARCH:
			throw new IllegalArgumentException("Cannot update Wildcards");
		default:
			throw new IllegalArgumentException("Unknown URI " + uri);
		}
		getContext().getContentResolver().notifyChange(uri, null);
		return count;
	}
	static {
		sUriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
		sUriMatcher.addURI(MediaLibrary.AUTHORITY, "media", MEDIA);
		sUriMatcher.addURI(MediaLibrary.AUTHORITY, "media/", MEDIA_UPC);
		sUriMatcher.addURI(MediaLibrary.AUTHORITY, "search/*", MEDIA_SEARCH);
		sUriMatcher.addURI(MediaLibrary.AUTHORITY, "search/", MEDIA);
		sMediaProjectionMap = new HashMap<String, String>();
		sMediaProjectionMap.put(Media.BARCODE, Media.BARCODE);
		sMediaProjectionMap.put(Media.TITLE, Media.TITLE);
		sMediaProjectionMap.put(Media.OWNED, Media.OWNED);
		sMediaProjectionMap.put(Media.LOANED, Media.LOANED);
	}
}
```