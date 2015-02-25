业务类 sqlite版本管理类
```  
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
[Tags]/**
 [Tags]* 
 [Tags]* 数据库版本控制类 SQLiteOpenHelper是一个数据库版本的控制超类
 [Tags]* 
 [Tags]*/
public class MangerDatabase extends SQLiteOpenHelper {
	private static final String name = "shool";
	private static final int version = 2;
	[Tags]/**
	 [Tags]* @param context 上下文信息
	 [Tags]* @param name 数据库名称
	 [Tags]* @param CursorFactory factory 游标工厂
	 [Tags]* @param version 数据库版本 执行数据参数的初始化工作
	 [Tags]*/
	public MangerDatabase(Context context) {
		// 调用超类的构造方法
		super(context, name, null, version);
	}
	[Tags]/**
	 [Tags]* 如果没有数据库中没有此表 就创建表结构 覆写超类的创建的方法 这个方法在超类中是一个只有方法体没有实现体的 onCreate 创建方法
	 [Tags]* 在用户执行调用获取用户数据库管理时例就已经执行 此方法是超类存在的 并且由 getWritableDatabase()这个方法调用的
	 [Tags]*/
	@Override
	public void onCreate(SQLiteDatabase db) {
		db.execSQL("CREATE TABLE person (personid integer primary key autoincrement, name varchar(20), age INTEGER,xxx INTEGER)");
	}
	[Tags]/**
	 [Tags]* 执行更新 如果表存在 将执行更新操作 oldVersion 老版本号 newVersio 新版本号 onUpgrade 创建方法
	 [Tags]* 在用户执行调用获取用户数据库管理时例就已经执行 此方法是超类存在的 并且由 getWritableDatabase()这个方法调用的
	 [Tags]*/
	@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		// 先删除
		db.execSQL("DROP TABLE IF EXISTS person");//
		// 调用方法执行创建
		onCreate(db);
	}
}
```