要在Android系统中操作SQLite数据库，是通过Android的核心类SQLiteDatabase类来实现的，通常情况下为了数据库升级的需要以及使用方便，我们会选择继承SQLiteOpenHelper抽像类，但是SQLiteOpenHelper会将数据库文件创建在一个固定的目录（内存的/data/data/<package name/databases>目录中），如果你想使用已经存在的数据库文件也就是说数据库会和程序一起发布，就得通过使用SQLiteDabase的静态方法OpenOrCreateDatabase()方法来得到SQLiteDabase对象，下面是一个具体操作类：
```  
import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import net.my.jokebook.R;
import android.app.Activity;
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
public class DBHelper {
	// 得到SD卡路径
	private final String DATABASE_PATH = android.os.Environment
			.getExternalStorageDirectory().getAbsolutePath() + "/joke";
	private final Activity activity;
	// 数据库名
	private final String DATABASE_FILENAME;
	public DBHelper(Context context) {
		// 这里直接给数据库名
		DATABASE_FILENAME = "jokebook.db3";
		activity = (Activity) context;
	}
	// 得到操作数据库的对象
	public SQLiteDatabase openDatabase() {
		try {
			boolean b = false;
			// 得到数据库的完整路径名
			String databaseFilename = DATABASE_PATH + "/" + DATABASE_FILENAME;
			// 将数据库文件从资源文件放到合适地方（资源文件也就是数据库文件放在项目的res下的raw目录中）
			// 将数据库文件复制到SD卡中 File dir = new File(DATABASE_PATH);
			if (!dir.exists())
				b = dir.mkdir();
			// 判断是否存在该文件
			if (!(new File(databaseFilename)).exists()) {
				// 不存在得到数据库输入流对象
				InputStream is = activity.getResources().openRawResource(
						R.raw.jokebook);
				// 创建输出流
				FileOutputStream fos = new FileOutputStream(databaseFilename);
				// 将数据输出
				byte[] buffer = new byte[8192];
				int count = 0;
				while ((count = is.read(buffer)) > 0) {
					fos.write(buffer, 0, count);
				}
				// 关闭资源
				fos.close();
				is.close();
			}
			// 得到SQLDatabase对象
			SQLiteDatabase database = SQLiteDatabase.openOrCreateDatabase(
					databaseFilename, null);
			return database;
		} catch (Exception e) {
			System.out.println(e.getMessage());
		}
		return null;
	}
}
```
写完这个类之后，就能得到SQLiteDatabase对象，就能对数据库操作了。