我们大家都知道让自己的数据和其它应用程序共享有两种方式：创建自己的Content Provider (即继承自Content Provider的子类)或者是将自己的数据添加到已有的Content Provider中去，后者需要保证现有的Content Provider和自己的数据类型相同并且具有该 Content Provider的写入的权限。 
如果需要创建一个Content Provider，则需要进行的工作主要分为以下3个步骤。 
(1) 建立数据的存储系统 
数据的存储系统可以由开发人员任意决定，一般来讲，大多数的Content Provider都通过Android的文件存储系统或SQLite数据库建立自己的数据存储系统。 
(2)扩展 ContentProvider类 
开发一个继承自ContentProvider类的子类代码来扩展ContentProvider类，在这个步骤主要的工作是将要共享的数据包装并以ContentResolver和Cursor对象能够访问到的形式对外展示。具体来说需要实现
ContentProvider 类中的6个抽象方法。 
```  
Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)：将查询的数据以Cursor 对象的形式返回。 
Uri insert(Uri uri, ContentValues values)：向 Content Provider中插入新数据记录，ContentValues 为数据记录的列名和列值映射。 
int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)：更新Content Provider中已存在的数据记录。 
int delete(Uri uri, String selection, String[] selectionArgs)：从Content Provider中删除数据记录。 
String getType(Uri uri)：返回Content Provider中的数据( MIME )类型。 
boolean onCreate()：当 Content Provider 启动时被调用。 
```
以上方法将会在ContentResolver对象中调用，所以很好地实现这些抽象方法会为ContentResolver提供一个完善的外部接口。除了实现抽象方法外，还可以做一些提高可用性的工作。 
定义一个 URI 类型的静态常量，命名为CONTENT_URI。 必须为该常量对象定义一个唯一的URI字符串，一般的做法是将ContentProvider子类的全称类名作为URI字符串，如：“自定义”。 
定义每个字段的列名，如果采用的数据库存储系统为SQLite 数据库，数据表列名可以采用数据库中表的列名。不管数据表中有没有其他的唯一标识一个记录的字段，都应该定义一个”_id”字段来唯一标识一个记录。模式使用 “INTEGER PRIMARY KEY AUTOINCREMENT” 自动更新 一般将这些列名字符串定义为静态常量, 如”_id”字段名定义为一个名为”_ID” 值为“_id”的静态字符串对象。 
(3)在应用程序的AdnroidManifest.xml 文件中声明Content Provider组件。 
创建好一个Content Provider必须要在应用程序的AndroidManifest.xml 中进行声明，否则该Content Provider对于Android系统将是不可见的。如果有一个名为MyProvider的类扩展了 ContentProvider类，声明该组件的代码如下：
```  
<provider name="自定义"
	authorities="wyf.wpf.myprovider"
...../> 
<!-- 为<provider>标记添加name、authorities属性--> 
```
其中name属性为ContentProvider子类的全称类名，authorities属性唯一标识了一个ContentProvider。还可以通过setReadPermission()和setWritePermission() 来设置其操作权限。当然也可以再上面的 xml中加入 android:readPermission 或者 android: writePermission属性来控制其权限。 
注意：因为ContentProvider可能被不同的进程和线程调用，所以这些方法必须是线程安全的。 
下边是一个例子修改了SDK中的Notes例子。首先创建ContentProvider的CONTENT_URI和一些字段数据，字段类可以继承自BaseColumns类，它包括了一些基本的字段，比如：_id等 代码如下：
```  
import android.net.Uri;
import android.provider.BaseColumns;
public class NotePad {
	// ContentProvider的uri
	public static final String AUTHORITY = "com.google.provider.NotePad";
	private NotePad() {
	}
	// 定义基本字段 实现BaseColumns 这个接口里边已经定义了"_id"字段所以这里不用定义了
	public static final class Notes implements BaseColumns {
		private Notes() {
		}
		// Uri.parse 方法根据指定字符串创建一个 Uri 对象
		public static final Uri CONTENT_URI = Uri.parse("content://
				+ AUTHORITY + "/notes");

		// 新的MIME类型-多个
		public static final String CONTENT_TYPE = "vnd.android.cursor.dir/vnd.google.note";
		// 新的MIME类型-单个
		public static final String CONTENT_ITME_TYPE = "vnd.android.cursor.item/vnd.google.note";
		public static final String DEFAULT_SORT_ORDER = "modified DESC";
		// 字段
		public static final String TITLE = "title";
		public static final String NOTE = "note";
		public static final String CREATEDDATE = "created";
		public static final String MODIFIEDDATE = "modified";
	}
}
```