我们知道数据库创建的时候默认保存到系统data/data/项目名下面了，有没有一种方法保存到自己指定的SDCard上的文件夹里面呢？答案是可以的。我们只需要找到SQLiteOpenHelper这个类，了解一下就不难发现其保存的路径是固定了的，那么我们只需要改动一下getWritableDatabase()，即写入的时候的路径：
```  
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
	if (mDatabase != null)
		mDatabase.lock();
	try {
		mIsInitializing = true;
		if (mName == null) {
			db = SQLiteDatabase.create(null);
		} else {
			db = mContext.openOrCreateDatabase(mName, 0, mFactory);
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
				mDatabase.unlock();
			}
			mDatabase = db;
		} else {
			if (mDatabase != null)
				mDatabase.unlock();
			if (db != null)
				db.close();
		}
	}
}
```
注意标志的红色的部分，这是创建数据库的地方，我们再看看openOrCreateDatabase()
这个方法，openOrCreateDatabase()这个方法存在于package android.content;下面的public abstract class Context{}类里面，
public abstract SQLiteDatabase openOrCreateDatabase(String name,int mode, CursorFactory factory);
我们发现在package android.database.sqlite;包下的public class SQLiteDatabase extends SQLiteClosable {}里面也有一个
```  
public static SQLiteDatabase openOrCreateDatabase(String path, CursorFactory factory) {
	return openDatabase(path, factory, CREATE_IF_NECESSARY);
}
```
相同的方法，只是参数不同，这个可以传一个路径进去，我们就调用这个方法。
然后我们还要有一个我们指定文件夹的路径的方法：
```  
public File getDatabasePath(String name) {
	String EXTERN_PATH = null;
	// 判断是否有SDcard
	if (Environment.getExternalStorageState().equals(
			Environment.MEDIA_MOUNTED) == true) {
		// 判断是否存在指定的文件夹，如果没有就创建它
		EXTERN_PATH = android.os.Environment.getExternalStorageDirectory()
				.getAbsolutePath() + "/database/";
		File f = new File(EXTERN_PATH);
		if (!f.exists()) {
			f.mkdirs();
		}
	}
	return new File(EXTERN_PATH + name);
}
```
及对SDcard进行写和读的权限：
```  
<!-- 在SDCard中创建于删除文件权限 -->  
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />  
<!-- 往SDCard写入数据权限 -->  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> 
```
最后当我们创建数据库的时候直接继承我们自己定义的SDSQLiteOpenHelper()，不用系统自带SQLiteOpenHelper()即可。
