Android应用程序可以使用文件或SqlLite数据库来存储数据。Content Provider提供了一种多应用间数据共享的方式，比如：联系人信息可以被多个应用程序访问。
Content Provider是个实现了一组用于提供其他应用程序存取数据的标准方法的类。
应用程序可以在Content Provider中执行如下操作：
```  
查询数据
修改数据
添加数据
删除数据
```
标准的Content Provider：
Android提供了一些已经在系统中实现的标准Content Provider，比如联系人信息，图片库等等，你可以用这些Content Provider来访问设备上存储的联系人信息，图片等等。
查询记录：
在Content Provider中使用的查询字符串有别于标准的SQL查询。很多诸如select，add，delete，modify等操作我们都使用一种特殊的URI来进行，这种URI由3个部分组成， "content：//"， 代表数据的路径，和一个可选的标识数据的ID。以下是一些示例URI：
```  
content：//media/internal/images 这个URI将返回设备上存储的所有图片
content：//contacts/people/ 这个URI将返回设备上的所有联系人信息
content：//contacts/people/45 这个URI返回单个结果(联系人信息中ID为45的联系人记录)
```
尽管这种查询字符串格式很常见，但是它看起来还是有点令人迷惑。为此，Android提供一系列的帮助类(在android.provider包下)，里面包含了很多以类变量形式给出的查询字符串，这种方式更容易让我们理解一点，参见下例：
```  
MediaStore.Images.Media.INTERNAL_CONTENT_URI
Contacts.People.CONTENT_URI
```
因此，如上面content：//contacts/people/45这个URI就可以写成如下形式：
```  
Uri person = ContentUris.withAppendedId(People.CONTENT_URI, 45);
```
然后执行数据查询：
```  
Cursor cur = managedQuery(person， null， null， null);
```
这个查询返回一个包含所有数据字段的游标，我们可以通过迭代这个游标来获取所有的数据：
```  
package com.wissen.testApp;
import android.R;
import android.app.Activity;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.provider.Contacts.People;
import android.widget.Toast;
public class ContentProviderDemo extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		displayRecords();
	}
	private void displayRecords() {
		// 该数组中包含了所有要返回的字段
		String columns[] = new String[] { People.NAME, People.NUMBER };
		Uri mContacts = People.CONTENT_URI;
		Cursor cur = managedQuery(mContacts, columns, // 要返回的数据字段
				null, // WHERE子句
				null, // WHERE 子句的参数
				null// Order-by子句
		);
		if (cur.moveToFirst()) {
			String name = null;
			String phoneNo = null;
			do {
				// 获取字段的值
				name = cur.getString(cur.getColumnIndex(People.NAME));
				phoneNo = cur.getString(cur.getColumnIndex(People.NUMBER));
				Toast.makeText(this, name + " " + phoneNo, Toast.LENGTH_LONG)
						.show();
			} while (cur.moveToNext());
		}
	}
}
```
