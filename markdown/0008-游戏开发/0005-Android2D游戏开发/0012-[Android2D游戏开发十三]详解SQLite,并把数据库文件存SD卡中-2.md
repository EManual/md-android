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
[Tags]/**
 [Tags]* @author android
 [Tags]* @保存方式：SQLite 轻量级数据库、
 [Tags]* @优点： 可以将自己的数据存储到文件系统或者数据库当中， 也可以将自己的数据存 储到SQLite数据库当中，还可以存到SD卡中
 [Tags]* @注意1：数据库对于一个游戏(一个应用)来说是私有的，并且在一个游戏当中， 数据库的名字也是唯一的。
 [Tags]* @注意2 apk中创建的数据库外部的进程是没有权限去读/写的, 我们需要把数据库文件创建到sdcard上可以解决类似问题.
 [Tags]* @注意3 当你删除id靠前的数据或者全部删除数据的时候，SQLite不会自动排序，
 [Tags]*      也就是说再添加数据的时候你不指定id那么SQLite默认还是在原有id最后添加一条新数据
 [Tags]* @注意4 android 中 的SQLite 语法区分大小写的!!!!!这点要注意！ String UPDATA_DATA =
 [Tags]*      "UPDATE android SET text='通过SQL语句来修改数据'  WHERE id=1"; 千万 不能可以写成 String
 [Tags]*      UPDATA_DATA = "updata android set text='通过SQL语句来修改数据'  where id=1";
 [Tags]*/
public class MainActivity extends Activity implements OnClickListener {
	private Button btn_addOne, btn_deleteone, btn_check, btn_deleteTable,
			btn_edit, btn_newTable;
	private TextView tv;
	private MySQLiteOpenHelper myOpenHelper;// 创建一个继承SQLiteOpenHelper类实例
	private SQLiteDatabase mysql;
	// ---------------以下两个成员变量是针对在SD卡中存储数据库文件使用
	// private File path = new File("/sdcard/android");// 创建目录
	// private File f = new File("/sdcard/android/android.db");// 创建文件
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
				// "INSERT INTO android (id,text) values (1, '通过SQL语句插入')";
				// db.execSQL(INSERT_DATA);
				tv.setText("添加数据成功！点击查看数据库查询");
			} else if (v == btn_deleteone) {// 删除数据
				// ---------------------- 读写句柄来删除
				mysql.delete("android", MySQLiteOpenHelper.ID + "=1", null);
				// 第一个参数 需要操作的表名
				// 第二个参数为 id+操作的下标 如果这里我们传入null，表示全部删除
				// 第三个参数默认传null即可
				// ----------------------- SQL语句来删除
				// String DELETE_DATA = "DELETE FROM android WHERE id=1";
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
				mysql.update("android", cv, "id " + "=" + Integer.toString(3),
						null);
				// ------------------------SQL语句来修改 -------------
				// String UPDATA_DATA =
				// "UPDATE android SET text='通过SQL语句来修改数据'  WHERE id=1";
				// db.execSQL(UPDATA_DATA);
				tv.setText("修改数据成功！点击查看数据库查询");
			} else if (v == btn_deleteTable) {// 删除表
				mysql.execSQL("DROP TABLE android");
				tv.setText("删除表成功！点击查看数据库查询");
			} else if (v == btn_newTable) {// 新建表
				String TABLE_NAME = "android";
				String ID = "id";
				String TEXT = "text";
				String str_sql2 = "CREATE TABLE " + TABLE_NAME + "(" + ID
						+ " INTEGER PRIMARY KEY AUTOINCREMENT," + TEXT
						+ " text );";
				mysql.execSQL(str_sql2);
				tv.setText("新建表成功！点击查看数据库查询");
			}
			// 删除数据库:
			// this.deleteDatabase("android.db");
		} catch (Exception e) {
			tv.setText("操作失败！");
		} finally {// 如果try中异常，也要对数据库进行关闭
			mysql.close();
		}
	}
}
```
以上代码中我们实现了两种存储方式:
一种存储默认系统路径/data-data-com.himi-databases下,另外一种则是保存在了/sdcard-himi下，生成数据库文件himi.db
那么这里两种实现方式大概步骤和区别说下：
-----------如果我们使用默认系统路径存储数据库文件：
第一步：新建一个类继承SQLiteOpenHelper;写一个构造，重写两个函数！
第二步：在新建的类中的onCreate(SQLiteDatabase db) 方法中创建一个表；
第三步：在进行删除数据、添加数据等操作的之前我们要得到数据库读写句柄得到一个数据库实例；
注意： 继承写这个辅助类，是为了在我们没有数据库的时候自动为我们生成一个数据库，并且生成数据库文件，这里也同时创建了一张表，因为我们在onCreate里是在数据库中创建一张表的操作；这里还要注意在我们new 这个我们这个MySQLiteOpenHelper 类实例对象的时候并没有创建数据库哟~！而是在我们调用 (备注3)MySQLiteOpenHelper ..getWritableDatabase() 这个方法得到数据库读写句柄的时候，android 会分析是否已经有了数据库，如果没有会默认为我们创建一个数据库并且在系统路径data-data-com.himi-databases下生成himi.db 文件!
(如果我们使用sd卡存储数据库文件，就没有必要写这个类了，而是我们自己Open自己的文件得到一个数据库,西西,反而方便~ )
-----------如果我们需要把数据库文件存储到SD卡中：
第一步：确认模拟器存在SD卡，关于SD卡的两种创建方法见我的博文：【Android 2D游戏开发之十】
第二步：（备注1）先创建SD卡目录和路径已经我们的数据库文件！这里不像上面默认路径中的那样，如果没有数据库会默认系统路径生成一个数据库和一个数据库文件！我们必须手动创建数据库文件！
第三步：在进行删除数据、添加数据等操作的之前我们要得到数据库读写句柄得到一个数据库实例；(备注2)此时的创建也不是像系统默认创建，而是我们通过打开第一步创建好的文件得到数据库实例。这里仅仅是创建一个数据库！！！！
第四步：在进行删除数据、添加数据等操作的之前我们还要创建一个表！
第五步：在配置文件AndroidMainfest.xml 声明写入SD卡的权限，上一篇已经介绍权限了，不知道的自己去看下吧。
有些童鞋不理解什么默认路径方式中就有表？那是因为我们在它默认给我们创建数据库的时候我们有创建表的操作，就是MySQLiteOpenHelper类中的onCreate()方法里的操作！所以我们如果要在进行删除数据、添加数据等操作的之前还要创建一个表，创建表的方法都是一样的。
总结：不管哪种方式我们都要-创建数据库-创建表-然后进行操作！
备注4：
在Android中查询数据是通过Cursor类来实现的，当我们使用SQLiteDatabase.query()方法时，会得到一个Cursor对象，Cursor指向的就是每一条数据。它提供了很多有关查询的方法，具体方法如下：
以下是方法和说明：
```  
move 以当前的位置为参考，将Cursor移动到指定的位置，成功返回true, 失败返回false
moveToPosition 将Cursor移动到指定的位置，成功返回true,失败返回false
moveToNext 将Cursor向前移动一个位置，成功返回true,失败返回false
moveToLast 将Cursor向后移动一个位置，成功返回true,失败返回 false。
movetoFirst 将Cursor移动到第一行，成功返回true,失败返回false
isBeforeFirst 返回Cursor是否指向第一项数据之前
isAfterLast  返回Cursor是否指向最后一项数据之后
isClosed 返回Cursor是否关闭
isFirst 
isLast 返回Cursor是否指向最后一项数据
isNull 返回指定位置的值是否为null
getCount  返回总的数据项数
getInt  返回当前行中指定的索引数据
```
对于SQLite的很多童鞋有接触过，但是就不知道怎么存储在SD中,所以我也研究了下,这篇也写了把sd卡中的方式也提供给大家。