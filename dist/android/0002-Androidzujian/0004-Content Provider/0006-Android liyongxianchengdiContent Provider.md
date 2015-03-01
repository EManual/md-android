#### 一、Content Provider 简介
我们说Android应用程序的四个核心组件是：Activity、Service、Broadcast Receiver 和 Content Provider。在Android中，应用程序彼此之间相互独立的，它们都运行在自己独立的虚拟机中。Content Provider提供了程序之间共享数据的方法，一个程序可以使用Content Provider定义一个URI，提供统一的操作接口，其他程序可以通过此URI访问指定的数据，进行数据的增、删、改、查。
#### 二、使用现成的Content Provider
下面我们就来看看代码，我们是怎么利用到现成的Content Provider。
```  
import android.R;
import android.app.Activity;
import android.content.ContentResolver;
import android.database.Cursor;
import android.os.Bundle;
import android.provider.ContactsContract;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.Toast;
public class MainContentProvider extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button b1 = (Button) findViewById(R.id.Button01);
		OnClickListener ocl = new OnClickListener() {
			@Override
			public void onClick(View v) {
				ContentResolver contentResolver = getContentResolver();
				// 获得所有的联系人
				Cursor cursor = contentResolver.query(
						ContactsContract.Contacts.CONTENT_URI, null, null,
						null, null);
				// 循环遍历
				if (cursor.moveToFirst()) {
					int idColumn = cursor
							.getColumnIndex(ContactsContract.Contacts._ID);
					int displayNameColumn = cursor
							.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME);
					do {
						// 获得联系人的ID号
						String contactId = cursor.getString(idColumn);
						// 获得联系人姓名
						String disPlayName = cursor
								.getString(displayNameColumn);
						Toast.makeText(MainContentProvider.this,
								"联系人姓名：" + disPlayName, Toast.LENGTH_LONG)
								.show();
						// 查看该联系人有多少个电话号码。如果没有这返回值为0
						int phoneCount = cursor
								.getInt(cursor
										.getColumnIndex(ContactsContract.Contacts.HAS_PHONE_NUMBER));
						if (phoneCount > 0) {
							// 获得联系人的电话号码列表
							Cursor phonesCursor = getContentResolver()
									.query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
											null,
											ContactsContract.CommonDataKinds.Phone.CONTACT_ID
													+ " = " + contactId, null,
											null);
							if (phonesCursor.moveToFirst()) {
								do {
									// 遍历所有的电话号码
									String phoneNumber = phonesCursor
											.getString(phonesCursor
													.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
									Toast.makeText(MainContentProvider.this,
											"联系人电话：" + phoneNumber,
											Toast.LENGTH_LONG).show();
								} while (phonesCursor.moveToNext());
							}
						}
					} while (cursor.moveToNext());
				}
			}
		};
		b1.setOnClickListener(ocl);
	}
}
```
效果图：
系统通讯录中的联系人信息

![img](http://emanual.github.io/md-android/img/component_provider/07_provider.jpg)  

我们的程序读取出来的联系人信息

![img](http://emanual.github.io/md-android/img/component_provider/07_provider2.jpg)   