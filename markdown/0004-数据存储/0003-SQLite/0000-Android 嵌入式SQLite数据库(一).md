#### SQLite特点
1.Android平台中嵌入了一个关系型数据库SQLite，和其他数据库不同的是SQLite存储数据时不区分类型。
例如一个字段声明为Integer类型，我们也可以将一个字符串存入，一个字段声明为布尔型，我们也可以存入浮点数。
除非是主键被定义为Integer，这时只能存储64位整数。
2.创建数据库的表时可以不指定数据类型，例如：
```  
CREATE TABLEperson(id INTEGER PRIMARY KEY, name)
```
3.SQLite支持大部分标准SQL语句，增删改查语句都是通用的，分页查询语句和MySQL相同。
```  
SELECT * FROMperson LIMIT 20 OFFSET 10
SELECT * FROMperson LIMIT 20, 10
```
#### 创建数据库
1.定义类继承SQLiteOpenHelper
2.声明构造函数，4个参数
3.重写onCreate()方法
4. 重写upGrade()方法
```  
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.database.sqlite.SQLiteDatabase.CursorFactory;
public class DBOpenHelper extends SQLiteOpenHelper {
	 /**
	  * 创建OpenHelper
	  * @param context 上下文
	  * @param name 数据库名
	  * @param factory 游标工厂
	  * @param version 数据库版本, 不要设置为0, 如果为0则会每次都创建数据库
	  */
	public DBOpenHelper(Context context, String name, CursorFactory factory,
			int version) {
		super(context, name, factory, version);
	}
	 /**
	  * 当数据库第一次创建的时候被调用
	  */
	public void onCreate(SQLiteDatabase db) {
		db.execSQL("CREATE TABLE person(id INTEGER PRIMARY KEY AUTOINCREMENT, name)");
	}
	 /**
	  * 当数据库版本发生改变的时候被调用
	  */
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		db.execSQL("ALTER TABLE person ADD balance");
	}
	public void testCreateDB() {
		DBOpenHelper helper = new DBOpenHelper(getContext(), "itcast.db", null,
				2);
		helper.getWritableDatabase(); // 创建数据库
	}
}
```