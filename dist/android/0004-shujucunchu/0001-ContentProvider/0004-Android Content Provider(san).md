下面我们要来创建一个Activity类，首先向其中插入两个数据，然后通过Toast来显示数据库中的数据。 运行效果如下：

![img](http://emanual.github.io/md-android/img/data_provider/05_datastore.jpg)
![img](http://emanual.github.io/md-android/img/data_provider/05_datastore2.jpg)

```  
import android.app.Activity;
import android.content.ContentValues;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.view.Gravity;
import android.widget.Toast;
public class Activity01 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		/* 插入数据 */
		ContentValues values = new ContentValues();
		values.put(NotePad.Notes.TITLE, "title1");
		values.put(NotePad.Notes.NOTE, "NOTENOTE1");
		getContentResolver().insert(NotePad.Notes.CONTENT_URI, values);
		values.clear();
		values.put(NotePad.Notes.TITLE, "title2");
		values.put(NotePad.Notes.NOTE, "NOTENOTE2");
		getContentResolver().insert(NotePad.Notes.CONTENT_URI, values);
		// 显示
		displayNote();
	}
	private void displayNote() {
		String columns[] = new String[] { NotePad.Notes._ID,
				NotePad.Notes.TITLE, NotePad.Notes.NOTE,
				NotePad.Notes.CREATEDDATE, NotePad.Notes.MODIFIEDDATE };
		Uri myUri = NotePad.Notes.CONTENT_URI;
		Cursor cur = managedQuery(myUri, columns, null, null, null);
		if (cur.moveToFirst()) {
			String id = null;
			String titile = null;
			do {
				id = cur.getString(cur.getColumnIndex(NotePad.Notes._ID));
				titile = cur.getString(cur.getColumnIndex(NotePad.Notes.TITLE));
				Toast toast = Toast.makeText(this, "TITILE:" + id + "\t
						+ "NOTE:" + titile, Toast.LENGTH_LONG);
				toast.setGravity(Gravity.TOP | Gravity.CENTER, 0, 40);
				toast.show();
			} while (cur.moveToNext());
		}
	}
}
```
最后不要忘记在AndroidManifest.xml文件中声明我们使用的ContentProvider,下面是我的AndroidManifest.xml文件
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="eoe.demo"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <provider
            android:name="NotePadProvider"
            android:authorities="com.xh.google.provider.NotePad" />
        <activity
            android:name=".Activity01"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <intent-filter>
                <data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
            </intent-filter>
            <intent-filter>
                <data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="5" />
</manifest>
```
