Android有一个独特之处就是，数据库只能被它的创建者所使用，其他的应用是不能访问到的，所以如果你想实现不同应用之间的数据共享，就不得不用content provider了。
在Android中，content provider是一个特殊的存储数据的类型，它提供了一套标准的接口用来获取以及操作数据.并且，android自身也提供了几个现成的content provider：Contacts，Browser，CallLog，Settings，MediaStore。
应用可以通过一个唯一的ContentResolver interface来使用具体的某个content provider。
```  
ContentResolver cr = getContentResolver();
```
然后你就可以用ContentResolver提供的方法来使用你需要的content provider了.其中contentResolver提供的方法包括query()，insert()，update()等.要使用这些方法，还会涉及到一个东西，那就是Uri。你可以将它理解成一个string形式的contentProvider的完全路径，它的形式为：
```  
<standard_prefix>://<authority>/<data_path>/<id>
```  
例如：
```  
content://browser/bookmarks
content://contacts/people
content://contacts/people/3
```
下面结合一个实例来看我们如何使用一个已有的content provider，给例子展示了如何从已有的电话本中读出联系人信息：
```  
import android.app.Activity;
import android.content.ContentResolver;
import android.database.Cursor;
import android.os.Bundle;
import android.provider.Contacts.People;
import android.util.Log;
import android.widget.Toast;
public class ContentProviderTest extends Activity {
	private final String TAG = "ContentProviderTest";
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		Log.i(TAG, "enter onCreate");
		setContentView(R.layout.main);
		createCP();
	}
	public void createCP() {
		ContentResolver cr = getContentResolver();
		Cursor cur = cr.query(People.CONTENT_URI, null, null, null, null);
		getColumnData(cur);
	}
	private void getColumnData(Cursor cur) {
		if (cur.moveToFirst()) {
			String name;
			String phoneNumber;
			int nameColumn = cur.getColumnIndex(People.NAME);
			int phoneColumn = cur.getColumnIndex(People.NUMBER);
			do {
				// Get the field values
				name = cur.getString(nameColumn);
				phoneNumber = cur.getString(phoneColumn);
				Log.i(TAG, "name=" + name);
				DisplayToast(name + " " + phoneNumber);
			} while (cur.moveToNext());
		}
	}
	public void DisplayToast(String s) {
		Toast.makeText(this, s, Toast.LENGTH_LONG).show();
	}
}
```