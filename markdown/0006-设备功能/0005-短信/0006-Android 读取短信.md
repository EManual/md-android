java代码：
```  
import android.app.Activity;
import android.database.Cursor;
import android.database.sqlite.SQLiteException;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;
public class mini extends Activity {
	[Tags]/** Called when the activity is first created. */
	private static final String LOG_TAG = "Sms Query";
	// 入口是onCreate
	@Override
	public void onCreate(Bundle savedInstanceState) {
		// super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
		super.onCreate(savedInstanceState);
		TextView tv = new TextView(this);
		tv.setText("Hello, Android");
		tv.setText(getSmsAndSendBack());
		setContentView(tv);
	}
	[Tags]/**
	 [Tags]* 读取短信
	 [Tags]* 
	 [Tags]* @return
	 [Tags]*/
	public String getSmsAndSendBack() {
		String[] projection = new String[] { "_id", "address", "person", "body" };
		StringBuilder str = new StringBuilder();
		try {
			Cursor myCursor = managedQuery(Uri.parse("content://sms/inbox"),
					projection, null, null, "date desc");
			str.append(processResults(myCursor, true));
			str.append("getContactsAndSendBack has executed!");
			/*
			 [Tags]* myCursor = managedQuery(Uri.parse("content://sms/inbox"), new
			 [Tags]* String[] { "_id", "address", "read" }, " address=? and read=?",
			 [Tags]* new String[] { "12345678901", "0" }, "date desc");
			 [Tags]*/
		} catch (SQLiteException ex) {
			Log.d(LOG_TAG, ex.getMessage());
		}
		return str.toString();
	}
	[Tags]/**
	 [Tags]* 处理短信结果
	 [Tags]* @param cur
	 [Tags]* @param all
	 [Tags]*            用来判断是读一条还是全部读。后来没有用all，可以无视
	 [Tags]*/
	private StringBuilder processResults(Cursor cur, boolean all) {
		StringBuilder str = new StringBuilder();
		if (cur.moveToFirst()) {
			String name;
			String phoneNumber;
			String sms;
			int nameColumn = cur.getColumnIndex("person");
			int phoneColumn = cur.getColumnIndex("address");
			int smsColumn = cur.getColumnIndex("body");
			do {
				// Get the field values
				name = cur.getString(nameColumn);
				phoneNumber = cur.getString(phoneColumn);
				sms = cur.getString(smsColumn);
				str.append("{");
				str.append(name + ",");
				str.append(phoneNumber + ",");
				str.append(sms);
				str.append("}");
				if (null == sms)
					sms = "";
				/*
				 [Tags]* if (all)
				 [Tags]* mView.loadUrl("javascript:navigator.SmsManager.droidAddContact('"
				 [Tags]* + name + "','" + phoneNumber + "','" + sms +"')"); else
				 [Tags]* mView.loadUrl("javascript:navigator.sms.droidFoundContact('"
				 [Tags]* + name + "','" + phoneNumber + "','" + sms +"')");
				 [Tags]*/
			} while (cur.moveToNext());
			/*
			 [Tags]* if (all)
			 [Tags]* mView.loadUrl("javascript:navigator.SmsManager.droidDone()");
			 [Tags]* else mView.loadUrl("javascript:navigator.sms.droidDone();");
			 [Tags]*/
		} else {
			str.append("no result!");
			/*
			 [Tags]* if(all) mView.loadUrl("javascript:navigator.SmsManager.fail()");
			 [Tags]* else
			 [Tags]* mView.loadUrl("javascript:navigator.sms.fail('None found!')");
			 [Tags]*/
		}
		return str;
	}// processResults
}
```
记得在AndroidManifest.xml中加入android.permission.READ_SMS这个permission
```  
<uses-permission android:name="android.permission.READ_SMS" />
```