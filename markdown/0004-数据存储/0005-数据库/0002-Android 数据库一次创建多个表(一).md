我们今天来说的就是android中的数据库（sqlite）一次性多建立几个表，这样我们就可以不会在用的时候在建立一张表，一次性我们建立多表以后，我们就省去很多的事情，那么我们还等什么，就来看看代码吧：
```  
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;
 /**
  * 默认就在数据库里创建4张表
  */
public class DBOpenHelper extends SQLiteOpenHelper {
	private static final String name = "database.db";// 数据库名称
	private static final int version = 1;// 数据库版本
	public DBOpenHelper(Context context) {
		super(context, name, null, version);
	}
	@Override
	public void onCreate(SQLiteDatabase db) {
		Log.e("DBOpenHelper",
				"DBOpenHelperDBOpenHelperDBOpenHelperDBOpenHelper");
		db.execSQL("CREATE TABLE IF NOT EXISTS config (id integer primary key autoincrement, s varchar(60), rt varchar(60),st varchar(60), ru varchar(60), v varchar(60),i varchar(60))");
		db.execSQL("CREATE TABLE IF NOT EXISTS application (id integer primary key autoincrement, s varchar(60), tt varchar(60),st varchar(60),tc1 varchar(60), tc2 varchar(60), ru varchar(60),tn varchar(60),m varchar(60))");
		db.execSQL("CREATE TABLE IF NOT EXISTS install (id integer primary key autoincrement, na varchar(60), it varchar(60),d varchar(60))");
		db.execSQL("CREATE TABLE IF NOT EXISTS smslist (id integer primary key autoincrement, t varchar(60), st varchar(60),n1 varchar(60),n2 varchar(60),n varchar(60),m varchar(60),a varchar(60))");
	}
	@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		Log.e("DBOpenHelper", "onUpgradeonUpgradeonUpgradeonUpgrade");
		db.execSQL("DROP TABLE IF EXISTS config");
		db.execSQL("DROP TABLE IF EXISTS application");
		db.execSQL("DROP TABLE IF EXISTS install");
		db.execSQL("DROP TABLE IF EXISTS smslist");
		onCreate(db);
	}
}
```
数据库服务
```  
import android.content.Context;
import android.database.Cursor;
import com.yangguangfu.bean.ApplicationInfo;
import com.yangguangfu.bean.ConfigInfo;
import com.yangguangfu.bean.InstallInfo;
import com.yangguangfu.bean.SMSInfo;
 /**
  * 数据库方法封装，创建表，删除表，数据(增删该查)...
  */
public class DatabaseService {
	private DBOpenHelper dbOpenHelper;
	public DatabaseService(Context context) {
		dbOpenHelper = new DBOpenHelper(context);
	}
	public void dropTable(String taleName) {
		dbOpenHelper.getWritableDatabase().execSQL(
				"DROP TABLE IF EXISTS " + taleName);
	}
	public void closeDatabase(String DatabaseName) {
		dbOpenHelper.getWritableDatabase().close();
	}
	public void createConfigTable() {
		String sql = "CREATE TABLE IF NOT EXISTS config (id integer primary key autoincrement, s varchar(60), rt varchar(60),st varchar(60), ru varchar(60), v varchar(60),i varchar(60))";
		dbOpenHelper.getWritableDatabase().execSQL(sql);
	}
	public void createTableApplication() {
		String sql = "CREATE TABLE IF NOT EXISTS application (id integer primary key autoincrement, s varchar(60), tt varchar(60),st varchar(60),tc1 varchar(60), tc2 varchar(60), ru varchar(60),tn varchar(60),m varchar(60))";
		dbOpenHelper.getWritableDatabase().execSQL(sql);
	}
	public void createTableInstall() {
		String sql = "CREATE TABLE IF NOT EXISTS install (id integer primary key autoincrement, na varchar(60), it varchar(60),d varchar(60))";
		dbOpenHelper.getWritableDatabase().execSQL(sql);
	}
	public void createTableSmslist() {
		String sql = "CREATE TABLE IF NOT EXISTS smslist (id integer primary key autoincrement, t varchar(60), st varchar(60),n1 varchar(60),n2 varchar(60),n varchar(60),m varchar(60),a varchar(60))";
		dbOpenHelper.getWritableDatabase().execSQL(sql);
	}
	public void saveConfigInfo(ConfigInfo configInfo) {
		dbOpenHelper.getWritableDatabase().execSQL(
				"insert into config (s, rt, st, ru, v,i) values(?,?,?,?,?,?)",
				new Object[] { configInfo.getS(), configInfo.getRt(),
						configInfo.getSt(), configInfo.getRu(),
						configInfo.getV(), configInfo.getI() });
	}
	public void saveApplicationInfo(ApplicationInfo configInfo) {
		dbOpenHelper
				.getWritableDatabase()
				.execSQL(
						"insert into application (s,tt,tc1,tc2,ru,tn,m) values(?,?,?,?,?,?,?)",
						new Object[] { configInfo.getS(), configInfo.getTt(),
								configInfo.getTc1(), configInfo.getTc2(),
								configInfo.getRu(), configInfo.getTn(),
								configInfo.getM() });
	}
	public void saveMsmInfo(SMSInfo configInfo) {
		dbOpenHelper
				.getWritableDatabase()
				.execSQL(
						"insert into smslist (t,st,n1,n2,n,m,a) values(?,?,?,?,?,?,?)",
						new Object[] { configInfo.getT(), configInfo.getSt(),
								configInfo.getN1(), configInfo.getN2(),
								configInfo.getN(), configInfo.getM(),
								configInfo.getA() });
	}
	public void saveInstallInfo(InstallInfo configInfo) {
		dbOpenHelper.getWritableDatabase().execSQL(
				"insert into install (na,it,d) values(?,?,?)",
				new Object[] { configInfo.getNa(), configInfo.getIt(),
						configInfo.getD() });
	}
	public void updateConfigInfo(ConfigInfo configInfo) {
		dbOpenHelper.getWritableDatabase().execSQL(
				"update config set s=?, rt=?, st=?, ru=?, v=?,i=? where id=?",
				new Object[] { configInfo.getS(), configInfo.getRt(),
						configInfo.getSt(), configInfo.getRu(),
						configInfo.getV(), configInfo.getI(),
						configInfo.getId() });
	}
	public void updateApplicationInfo(ApplicationInfo configInfo) {
		dbOpenHelper
				.getWritableDatabase()
				.execSQL(
						"update application set s=?, tt=?, st=?, tc1=?, tc2=?,ru=?,tn=?,m=? where id=?",
						new Object[] { configInfo.getS(), configInfo.getTt(),
								configInfo.getSt(), configInfo.getTc1(),
								configInfo.getTc2(), configInfo.getRu(),
								configInfo.getTn(), configInfo.getM(),
								configInfo.getId() });
	}
	public void updateInstallInfo(InstallInfo configInfo) {
		dbOpenHelper.getWritableDatabase().execSQL(
				"update install set na=?, it=?, d=? where id=?",
				new Object[] { configInfo.getNa(), configInfo.getIt(),
						configInfo.getD(), configInfo.getId() });
	}
	public void updateSMSInfo(SMSInfo configInfo) {
		dbOpenHelper
				.getWritableDatabase()
				.execSQL(
						"update smslist set t=?, st=?, n1=?, n2=?, n=?, m=?, a=? where id=?",
						new Object[] { configInfo.getT(), configInfo.getSt(),
								configInfo.getN1(), configInfo.getN2(),
								configInfo.getN(), configInfo.getM(),
								configInfo.getA(), configInfo.getId() });
	}
}
```
