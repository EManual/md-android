SQLite的特性：
#### 轻量级
使用 SQLite 只需要带一个动态库，就可以享受它的全部功能，而且那个动态库的尺寸想当小。
#### 独立性
SQLite 数据库的核心引擎不需要依赖第三方软件，也不需要所谓的“安装”。
#### 隔离性
SQLite 数据库中所有的信息（比如表、视图、触发器等）都包含在一个文件夹内，方便管理和维护。
#### 跨平台
SQLite 目前支持大部分操作系统，不至电脑操作系统更在众多的手机系统也是能够运行，比如：Android。
#### 多语言接口
SQLite 数据库支持多语言编程接口。
#### 安全性
SQLite 数据库通过数据库级上的独占性和共享锁来实现独立事务处理。这意味着多个进程可以在同一时间从同一数据库读取数据，但只能有一个可以写入数据。
优点：
1.能存储较多的数据。
2.能将数据库文件存放到SD卡中！
#### 什么是 SQLiteDatabase？ 
一个SQLiteDatabase的实例代表了一个SQLite的数据库，通过SQLiteDatabase实例的一些方法，我们可以执行SQL语句，对数据库进行增、删、查、改的操作。需要注意的是，数据库对于一个应用来说是私有的，并且在一个应用当中，数据库的名字也是惟一的。
#### 什么是 SQLiteOpenHelper ？
根据这名字，我们可以看出这个类是一个辅助类。这个类主要生成一个数据库，并对数据库的版本进行管理。当在程序当中调用这个类的方法getWritableDatabase()，或者getReadableDatabase()方法的时候，如果当时没有数据，那么Android系统就会自动生成一个数据库。SQLiteOpenHelper是一个抽象类，我们通常需要继承它，并且实现里边的3 个函数，
#### 什么是 ContentValues 类？
ContentValues 类和Hashmap/Hashtable 比较类似，它也是负责存储一些名值对，但是它存储的名值对当中的名是一个String 类型，而值都是基本类型。
#### 什么是 Cursor ？
Cursor 在Android 当中是一个非常有用的接口，通过Cursor 我们可以对从数据库查询出来的结果集进行随机的读写访问。
xml中定义了我们需要练习用到的几个操作按钮，这里不多解释了，下面看java源码：先看我们继承的 SQLiteOpenHelper 类
```  
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import android.util.Log;
 /**
  * @解释 此类我们只需要传建一个构造函数 以及重写两个方法就OK啦、
  */
public class MySQLiteOpenHelper extends SQLiteOpenHelper {
	public final static int VERSION = 1;// 版本号
	public final static String TABLE_NAME = "himi";// 表名
	public final static String ID = "id";// 后面ContentProvider使用
	public final static String TEXT = "text";
	public static final String DATABASE_NAME = "Himi.db";
	public MySQLiteOpenHelper(Context context) {
		// 在Android 中创建和打开一个数据库都可以使用openOrCreateDatabase 方法来实现，
		// 因为它会自动去检测是否存在这个数据库，如果存在则打开，不过不存在则创建一个数据库；
		// 创建成功则返回一个 SQLiteDatabase对象，否则抛出异常FileNotFoundException。
		// 下面是来创建一个名为"DATABASE_NAME"的数据库，并返回一个SQLiteDatabase对象
		super(context, DATABASE_NAME, null, VERSION);
	}
	@Override
	// 在数据库第一次生成的时候会调用这个方法，一般我们在这个方法里边生成数据库表;
	public void onCreate(SQLiteDatabase db) {
		String str_sql = "CREATE TABLE " + TABLE_NAME + "(" + ID
				+ " INTEGER PRIMARY KEY AUTOINCREMENT," + TEXT + " text );";
		// CREATE TABLE 创建一张表 然后后面是我们的表名
		// 然后表的列，第一个是id 方便操作数据,int类型
		// PRIMARY KEY 是指主键 这是一个int型,用于唯一的标识一行;
		// AUTOINCREMENT 表示数据库会为每条记录的key加一，确保记录的唯一性;
		// 最后我加入一列文本 String类型
		// ----------注意：这里str_sql是sql语句，类似dos命令，要注意空格！
		db.execSQL(str_sql);
		// execSQL()方法是执行一句sql语句
		// 虽然此句我们生成了一张数据库表和包含该表的sql.himi文件,
		// 但是要注意 不是方法是创建，是传入的一句str_sql这句sql语句表示创建！！
	}
	@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		// 一般默认情况下，当我们插入 数据库就立即更新
		// 当数据库需要升级的时候，Android 系统会主动的调用这个方法。
		// 一般我们在这个方法里边删除数据表，并建立新的数据表，
		// 当然是否还需要做其他的操作，完全取决于游戏需求。
		Log.v("Himi", "onUpgrade");
	}
}
```
下面看最重要的MainActivity中的代码：
```  
import java.io.File;
import java.io.IOException;
import android.app.Activity;
import android.content.ContentValues;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.view.WindowManager;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;
// ------------第三种保存方式--------《SQLite》---------
 /**
  * @保存方式：SQLite 轻量级数据库、
  * @优点： 可以将自己的数据存储到文件系统或者数据库当中， 也可以将自己的数据存 储到SQLite数据库当中，还可以存到SD卡中
  * @注意1：数据库对于一个游戏(一个应用)来说是私有的，并且在一个游戏当中， 数据库的名字也是唯一的。
  * @注意2 apk中创建的数据库外部的进程是没有权限去读/写的, 我们需要把数据库文件创建到sdcard上可以解决类似问题.
  * @注意3 当你删除id靠前的数据或者全部删除数据的时候，SQLite不会自动排序，
  *      也就是说再添加数据的时候你不指定id那么SQLite默认还是在原有id最后添加一条新数据
  * @注意4 android 中 的SQLite 语法区分大小写的!!!!!这点要注意！ String UPDATA_DATA =
  *      "UPDATE himi SET text='通过SQL语句来修改数据'  WHERE id=1"; 千万 不能可以写成 String
  *      UPDATA_DATA = "updata himi set text='通过SQL语句来修改数据'  where id=1";
  */
public class MainActivity extends Activity implements OnClickListener {
	private Button btn_addOne, btn_deleteone, btn_check, btn_deleteTable,
			btn_edit, btn_newTable;
	private TextView tv;
	private MySQLiteOpenHelper myOpenHelper;// 创建一个继承SQLiteOpenHelper类实例
	private SQLiteDatabase mysql;
	// ---------------以下两个成员变量是针对在SD卡中存储数据库文件使用
	// private File path = new File("/sdcard/himi");// 创建目录
	// private File f = new File("/sdcard/himi/himi.db");// 创建文件
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.main);
		tv = (TextView) findViewById(R.id.tv_title);
		btn_addOne = (Button) findViewById(R.id.sql_addOne);
		btn_check = (Button) findViewById(R.id.sql_check);
		btn_deleteone = (Button) findViewById(R.id.sql_deleteOne);
		btn_deleteTable = (Button) findViewById(R.id.sql_deleteTable);
		btn_newTable = (Button) findViewById(R.id.sql_newTable);
		btn_edit = (Button) findViewById(R.id.sql_edit);
		btn_edit.setOnClickListener(this);
		btn_addOne.setOnClickListener(this);
		btn_check.setOnClickListener(this);
		btn_deleteone.setOnClickListener(this);
		btn_deleteTable.setOnClickListener(this);
		btn_newTable.setOnClickListener(this);
		myOpenHelper = new MySQLiteOpenHelper(this);// 实例一个数据库辅助器
		// 备注1 ----如果你使用的是将数据库的文件创建在SD卡中，那么创建数据库mysql如下操作：
		// if (!path.exists()) {// 目录存在返回false
		// path.mkdirs();// 创建一个目录
		// }
		// if (!f.exists()) {// 文件存在返回false
		// try {
		// f.createNewFile();//创建文件
		// } catch (IOException e) {
		// e.printStackTrace();
		// }
		// }
	}
	@Override
	public void onClick(View v) {
		try {
			// 备注2----如果你使用的是将数据库的文件创建在SD卡中，那么创建数据库mysql如下操作：
			// mysql = SQLiteDatabase.openOrCreateDatabase(f, null);
			// 备注3--- 如果想把数据库文件默认放在系统中,那么创建数据库mysql如下操作：
			mysql = myOpenHelper.getWritableDatabase(); // 实例数据库
			if (v == btn_addOne) {// 添加数据
				// ---------------------- 读写句柄来插入---------
				// ContentValues 其实就是一个哈希表HashMap， key值是字段名称，
				// Value值是字段的值。然后 通过 ContentValues 的 put 方法就可以
				// 把数据放到ContentValues中，然后插入到表中去!
				ContentValues cv = new ContentValues();
				cv.put(MySQLiteOpenHelper.TEXT, "测试新的数据");
				mysql.insert(MySQLiteOpenHelper.TABLE_NAME, null, cv);
				// inser() 第一个参数 标识需要插入操作的表名
				// 第二个参数 :默认传null即可
				// 第三个是插入的数据
				// ---------------------- SQL语句插入--------------
				// String INSERT_DATA =
				// "INSERT INTO himi (id,text) values (1, '通过SQL语句插入')";
				// db.execSQL(INSERT_DATA);
				tv.setText("添加数据成功！点击查看数据库查询");
			} else if (v == btn_deleteone) {// 删除数据
				// ---------------------- 读写句柄来删除
				mysql.delete("himi", MySQLiteOpenHelper.ID + "=1", null);
				// 第一个参数 需要操作的表名
				// 第二个参数为 id+操作的下标 如果这里我们传入null，表示全部删除
				// 第三个参数默认传null即可
				// ----------------------- SQL语句来删除
				// String DELETE_DATA = "DELETE FROM himi WHERE id=1";
				// db.execSQL(DELETE_DATA);
				tv.setText("删除数据成功！点击查看数据库查询");
			} else if (v == btn_check) {// 遍历数据
				// 备注4------
				Cursor cur = mysql.rawQuery("SELECT * FROM
						+ MySQLiteOpenHelper.TABLE_NAME, null);
				if (cur != null) {
					String temp = "";
					int i = 0;
					while (cur.moveToNext()) {// 直到返回false说明表中到了数据末尾
						temp += cur.getString(0);
						// 参数0 指的是列的下标,这里的0指的是id列
						temp += cur.getString(1);
						// 这里的0相对于当前应该是咱们的text列了
						i++;
						temp += "  "; // 这里是我整理显示格式 ,呵呵~
						if (i % 3 == 0) // 这里是我整理显示格式 ,呵呵~
							temp += "\n";// 这里是我整理显示格式 ,呵呵~
					}
					tv.setText(temp);
				}
			} else if (v == btn_edit) {// 修改数据
				// ------------------------句柄方式来修改 -------------
				ContentValues cv = new ContentValues();
				cv.put(MySQLiteOpenHelper.TEXT, "修改后的数据");
				mysql.update("himi", cv, "id " + "=" + Integer.toString(3),
						null);
				// ------------------------SQL语句来修改 -------------
				// String UPDATA_DATA =
				// "UPDATE himi SET text='通过SQL语句来修改数据'  WHERE id=1";
				// db.execSQL(UPDATA_DATA);
				tv.setText("修改数据成功！点击查看数据库查询");
			} else if (v == btn_deleteTable) {// 删除表
				mysql.execSQL("DROP TABLE himi");
				tv.setText("删除表成功！点击查看数据库查询");
			} else if (v == btn_newTable) {// 新建表
				String TABLE_NAME = "himi";
				String ID = "id";
				String TEXT = "text";
				String str_sql2 = "CREATE TABLE " + TABLE_NAME + "(" + ID
						+ " INTEGER PRIMARY KEY AUTOINCREMENT," + TEXT
						+ " text );";
				mysql.execSQL(str_sql2);
				tv.setText("新建表成功！点击查看数据库查询");
			}
			// 删除数据库:
			// this.deleteDatabase("himi.db");
		} catch (Exception e) {
			tv.setText("操作失败！");
		} finally {// 如果try中异常，也要对数据库进行关闭
			mysql.close();
		}
	}
}
```