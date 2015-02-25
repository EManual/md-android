在这个例子里，我们将要创建一个新的子Activity来响应联系人数据的PICK_ACTION动作。它显示联系人数据库中每个联系人，允许用户选择其中一个，在关闭之前返回它的URI给调用方的Activity。Android已经提供了一个Intent Filter来从一个列表中挑选一个联系人，而且被隐式Intent（包含着content://contacts/people/的URI）触发。这个练习的目的是演示这种形式，即使这个特定的实现并不有用。
1. 创建一个新的ContactPicker工程，包含ContactPicker Activity。
```  
import android.R;
import android.app.Activity;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.provider.Contacts.People;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.ListView;
import android.widget.SimpleCursorAdapter;
public class ContactPicker extends Activity {
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.main);
	}
}
```
2. 修改main.xml layout资源，包含一个ListView控件。这个控件用来显示联系人。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ListView
        android:id="@+id/contactListView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
3. 创建一个新的listitemlayout.xml layout资源，包含一个TextView。它用来显示ListView中的单个联系人。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/itemTextView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:padding="10px"
        android:textColor="FFF"
        android:textSize="16px" />
</LinearLayout>
```
4. 返回到ContactPicker Activity中。重写onCreate方法，从隐式Intent中释放path数据。
```  
@Override
public void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	setContentView(R.layout.main);
	Intent intent = getIntent();
	String dataPath = intent.getData().toString();
}
```
4.1. 为储存在联系人列表中的人创建一个新的URI，并且使用一个SimpleCursorAdapter将其与ListView绑定。SimpleCursorAdapter允许你通过Content Provider设定游标数据给View。在这里使用时没有什么提示。
```  
final Uri data = Uri.parse(dataPath + "people/");
final Cursor c = managedQuery(data, null, null, null, null);
String[] from = new String[] {People.NAME};
int[] to = new int[] { R.id.itemTextView };
SimpleCursorAdapter adapter = new SimpleCursorAdapter(this, R.layout.listitemlayout, c, from, to);
ListView lv = (ListView)findViewById(R.id.contactListView);
lv.setAdapter(adapter);
```
4.2. 给ListView添加一个ItemClickListener 。从列表中选择一个联系人时，必须返回一个path给调用方Activity。
```  
lv.setOnItemClickListener(new OnItemClickListener() {
	public void onItemClick(AdapterView<?> parent, View view, int pos, long id) {
		// 移动光标到所选择的项目
		c.moveToPosition(pos);
		// 提取排id.
		int rowId = c.getInt(c.getColumnIndexOrThrow("_id"));
		//构建结果URI.
		Uri outURI = Uri.parse(data.toString() + rowId);
		Intent outData = new Intent();
		outData.setData(outURI);
		setResult(Activity.RESULT_OK, outData);
		finish();
	}
});
```
关闭onCreate方法.这个很重要大家要记住。
5. 修改应用程序的manifest，替换Activity的intent-filter标签，增加对联系人数据应用PICK动作的支持。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.paad.contactpicker" >
    <application android:icon="@drawable/icon" >
        <activity
            android:name="ContactPicker"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.PICK" />
                <category android:name="android.intent.category.DEFAULT" />
                <data
                    android:path="contacts"
                    android:scheme="content" >
                </data>
            </intent-filter>
        </activity>
    </application>
</manifest>
```
6. 这就完成子Activity。为了测试它，创建一个新的测试ContentPickerTester Activity。创建一个新的layout资源(contentpickertester)，包含一个TextView显示选择的联系人，一个Button用来启动子Activity。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/selected_contact_textview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <Button
        android:id="@+id/pick_contact_button"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Pick Contact" />
</LinearLayout>
```
7. 重写ContentPickerTester的onCreate方法，给Button增加一个Click listener，这样，它就能通过指定PICK_ACTION和联系人数据库URI(content://contacts/)来隐式启动一个子Activity。
```  
import android.R;
import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class ContentPickerTester extends Activity {
	public static final int PICK_CONTACT = 1;
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.contentpickertester);
		Button button = (Button) findViewById(R.id.pick_contact_button);
		button.setOnClickListener(new OnClickListener() {
			public void onClick(View _view) {
				Intent intent = new Intent(Intent.ACTION_PICK, Uri
						.parse("content://contacts/"));
				startActivityForResult(intent, PICK_CONTACT);
			}
		});
	}
}
```
8. 当子Activity返回时，使用返回值来将选择的联系人姓名填入TextView。
```  
@Override
public void onActivityResult(int reqCode, int resCode, Intent data) {
	super.onActivityResult(reqCode, resCode, data);
	switch (reqCode) {
	case (PICK_CONTACT): {
		if (resCode == Activity.RESULT_OK) {
			Uri contactData = data.getData();
			Cursor c = managedQuery(contactData, null, null, null, null);
			c.moveToFirst();
			String name;
			name = c.getString(c.getColumnIndexOrThrow(People.NAME));
			TextView tv;
			tv = (TextView) findViewById(R.id.selected_contact_textview);
			tv.setText(name);
		}
		break;
	}
	}
}
```
9.当你的测试类完成后，简单地把它增加到应用程序的manifest中。你还需要增加一个uses-permission标签，添加READ_CONTACTS权限，来运行应用程序读取联系人数据库。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.paad.contactpicker" >
    <application android:icon="@drawable/icon" >
        <activity
            android:name=".ContactPicker"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.PICK" />
                <category android:name="android.intent.category.DEFAULT" />
                <data
                    android:path="contacts"
                    android:scheme="content" />
            </intent-filter>
        </activity>
        <activity
            android:name=".ContentPickerTester"
            android:label="Contact Picker Test" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-permission android:name="android.permission.READ_CONTACTS" />
</manifest>
```
效果图：
![img](P)  
一旦你选择了一个联系人，父Activity会返回到前台，并且显示选择的联系人姓名
![img](P)  