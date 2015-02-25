最近碰到apk和后台的cpp code都需要访问一个数据库的问题。结果发现apk中创建的数据库外部的进程是没有权限去读/写的。这就需要把数据库文件创建到sdcard上。
后来发现在SQLiteOpenHelper(frameworks/base/core/java/android/database/sqlite/SQLiteOpenHelper.java)这个类中，创建数据库文件的路径是使用传入的contex的getDatabasePath获取的，这个是不允许修改的。
那我就仿照这个SQLiteOpenHelper写了一个abstract class SDSQLiteOpenHelper，其使用和SQLiteOpenHelper一样，然后只要加上相应的permission，这样就可以实现把数据库存储到sdcard上了。
```  
import java.io.File;
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteException;
import android.database.sqlite.SQLiteDatabase.CursorFactory;
import android.util.Log;
[Tags]/**
 [Tags]* A helper class to manage database creation and version management. You create
 [Tags]* a subclass implementing {@link onCreate}, {@link onUpgrade} and optionally
 [Tags]* {@link onOpen}, and this class takes care of opening the database if it
 [Tags]* exists, creating it if it does not, and upgrading it as necessary.
 [Tags]* Transactions are used to make sure the database is always in a sensible
 [Tags]* state.
 [Tags]* <p>
 [Tags]* For an example, see the NotePadProvider class in the NotePad sample
 [Tags]* application, in the <em>samples/</em> directory of the SDK.
 [Tags]* </p>
 [Tags]*/
public abstract class SDSQLiteOpenHelper {
	private static final String TAG = SDSQLiteOpenHelper.class.getSimpleName();
	private final Context mContext;
	private final String mName;
	private final CursorFactory mFactory;
	private final int mNewVersion;
	private SQLiteDatabase mDatabase = null;
	private boolean mIsInitializing = false;
	[Tags]/**
	 [Tags]* Create a helper object to create, open, and/or manage a database. The
	 [Tags]* database is not actually created or opened until one of
	 [Tags]* {@link getWritableDatabase} or {@link getReadableDatabase} is called.
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]*            to use to open or create the database
	 [Tags]* @param name
	 [Tags]*            of the database file, or null for an in-memory database
	 [Tags]* @param factory
	 [Tags]*            to use for creating cursor objects, or null for the default
	 [Tags]* @param version
	 [Tags]*            number of the database (starting at 1); if the database is
	 [Tags]*            older, {@link onUpgrade} will be used to upgrade the database
	 [Tags]*/
	public SDSQLiteOpenHelper(Context context, String name,
			CursorFactory factory, int version) {
		if (version < 1)
			throw new IllegalArgumentException("Version must be >= 1, was 
					+ version);
		mContext = context;
		mName = name;
		mFactory = factory;
		mNewVersion = version;
	}
	[Tags]/**
	 [Tags]* Create and/or open a database that will be used for reading and writing.
	 [Tags]* Once opened successfully, the database is cached, so you can call this
	 [Tags]* method every time you need to write to the database. Make sure to call
	 [Tags]* {@link close} when you no longer need it.
	 [Tags]* 
	 [Tags]* <p>
	 [Tags]* Errors such as bad permissions or a full disk may cause this operation to
	 [Tags]* fail, but future attempts may succeed if the problem is fixed.
	 [Tags]* </p>
	 [Tags]* 
	 [Tags]* @throws SQLiteException
	 [Tags]*             if the database cannot be opened for writing
	 [Tags]* @return a read/write database object valid until {@link close} is called
	 [Tags]*/
	public synchronized SQLiteDatabase getWritableDatabase() {
		if (mDatabase != null && mDatabase.isOpen() && !mDatabase.isReadOnly()) {
			return mDatabase; // The database is already open for business
		}
		if (mIsInitializing) {
			throw new IllegalStateException(
					"getWritableDatabase called recursively");
		}
		// If we have a read-only database open, someone could be using it
		// (though they shouldn't), which would cause a lock to be held on
		// the file, and our attempts to open the database read-write would
		// fail waiting for the file lock. To prevent that, we acquire the
		// lock on the read-only database, which shuts out other users.
		boolean success = false;
		SQLiteDatabase db = null;
		try {
			mIsInitializing = true;
			if (mName == null) {
				db = SQLiteDatabase.create(null);
			} else {
				String path = getDatabasePath(mName).getPath();
				db = SQLiteDatabase.DatabopenOrCreatease(path, mFactory);
			}
			int version = db.getVersion();
			if (version != mNewVersion) {
				db.beginTransaction();
				try {
					if (version == 0) {
						onCreate(db);
					} else {
						onUpgrade(db, version, mNewVersion);
					}
					db.setVersion(mNewVersion);
					db.setTransactionSuccessful();
				} finally {
					db.endTransaction();
				}
			}
			onOpen(db);
			success = true;
			return db;
		} finally {
			mIsInitializing = false;
			if (success) {
				if (mDatabase != null) {
					try {
						mDatabase.close();
					} catch (Exception e) {
					}
				}
				mDatabase = db;
			} else {
				if (db != null)
					db.close();
			}
		}
	}
	[Tags]/**
	 [Tags]* Create and/or open a database. This will be the same object returned by
	 [Tags]* {@link getWritableDatabase} unless some problem, such as a full disk,
	 [Tags]* requires the database to be opened read-only. In that case, a read-only
	 [Tags]* database object will be returned. If the problem is fixed, a future call
	 [Tags]* to {@link getWritableDatabase} may succeed, in which case the read-only
	 [Tags]* database object will be closed and the read/write object will be returned
	 [Tags]* in the future.
	 [Tags]* 
	 [Tags]* @throws SQLiteException
	 [Tags]*             if the database cannot be opened
	 [Tags]* @return a database object valid until {@link getWritableDatabase} or
	 [Tags]*         {@link close} is called.
	 [Tags]*/
	public synchronized SQLiteDatabase getReadableDatabase() {
		if (mDatabase != null && mDatabase.isOpen()) {
			return mDatabase; // The database is already open for business
		}
		if (mIsInitializing) {
			throw new IllegalStateException(
					"getReadableDatabase called recursively");
		}
		try {
			return getWritableDatabase();
		} catch (SQLiteException e) {
			if (mName == null)
				throw e; // Can't open a temp database read-only!
			Log.e(TAG, "Couldn't open " + mName
					+ " for writing (will try read-only):", e);
		}
		SQLiteDatabase db = null;
		try {
			mIsInitializing = true;
			String path = getDatabasePath(mName).getPath();
			db = SQLiteDatabase.openDatabase(path, mFactory,
					SQLiteDatabase.OPEN_READWRITE);
			if (db.getVersion() != mNewVersion) {
				throw new SQLiteException(
						"Can't upgrade read-only database from version 
								+ db.getVersion() + " to " + mNewVersion + ":
								+ path);
			}
			onOpen(db);
			Log.w(TAG, "Opened " + mName + " in read-only mode");
			mDatabase = db;
			return mDatabase;
		} finally {
			mIsInitializing = false;
			if (db != null && db != mDatabase)
				db.close();
		}
	}
	[Tags]/**
	 [Tags]* Close any open database object.
	 [Tags]*/
	public synchronized void close() {
		if (mIsInitializing)
			throw new IllegalStateException("Closed during initialization");
		if (mDatabase != null && mDatabase.isOpen()) {
			mDatabase.close();
			mDatabase = null;
		}
	}
	public File getDatabasePath(String name) {
		return new File("/sdcard/" + name);
	}
	[Tags]/**
	 [Tags]* Called when the database is created for the first time. This is where the
	 [Tags]* creation of tables and the initial population of the tables should
	 [Tags]* happen.
	 [Tags]* 
	 [Tags]* @param db
	 [Tags]*            The database.
	 [Tags]*/
	public abstract void onCreate(SQLiteDatabase db);
	[Tags]/**
	 [Tags]* Called when the database needs to be upgraded. The implementation should
	 [Tags]* use this method to drop tables, add tables, or do anything else it needs
	 [Tags]* to upgrade to the new schema version.
	 [Tags]* 
	 [Tags]* <p>
	 [Tags]* The SQLite ALTER TABLE documentation can be found <a
	 [Tags]* href="http://sqlite.org/lang_altertable.html">here</a>. If you add new
	 [Tags]* columns you can use ALTER TABLE to insert them into a live table. If you
	 [Tags]* rename or remove columns you can use ALTER TABLE to rename the old table,
	 [Tags]* then create the new table and then populate the new table with the
	 [Tags]* contents of the old table.
	 [Tags]* 
	 [Tags]* @param db
	 [Tags]*            The database.
	 [Tags]* @param oldVersion
	 [Tags]*            The old database version.
	 [Tags]* @param newVersion
	 [Tags]*            The new database version.
	 [Tags]*/
	public abstract void onUpgrade(SQLiteDatabase db, int oldVersion,
			int newVersion);
	[Tags]/**
	 [Tags]* Called when the database has been opened. Override method should check
	 [Tags]* {@link SQLiteDatabaseisReadOnly} before updating the database.
	 [Tags]* 
	 [Tags]* @param db
	 [Tags]*            The database.
	 [Tags]*/
	public void onOpen(SQLiteDatabase db) {
	}
}
```