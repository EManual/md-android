在我们开发项目的有些时候，采用Android的layout布局有时候根本满足不了我们对于界面的要求，有时候没有web页面那样炫。其实android也可以采用最原始的html页面来进行布局，这样我们可以修改html页面来满足我们的需求，达到一个很炫的效果。android中我们也可以利用javascript来帮我们实现一些很简单的应用。其他的话不多说啦，直接开始项目的介绍吧。
1.我们需要将我们的html页面放入andorid中的assets文件夹下，然后新建一个images文件夹，放入一张图片。
![img](P)  
这个是我们放好页面后的工程目录。index.html的代码如下：
```  
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
<script type="text/javascript">
function show(jsondata){
	var jsonobjs = eval(jsondata);
	var table = document.getElementById("personTable");
	for(var y=0; y<jsonobjs.length; y++){
		var tr = table.insertRow(table.rows.length); //添加一行
		//添加三列
		var td1 = tr.insertCell(0);
		var td2 = tr.insertCell(1);
		td2.align = "center";
		var td3 = tr.insertCell(2);
		td3.align = "center";
		//设置列内容和属性
		td1.innerHTML = jsonobjs[y].id;
		td2.innerHTML = jsonobjs[y].name;
		td3.innerHTML = "<a href='javascript:contact.call(\""+ jsonobjs[y].mobile+ "\")'>"+ jsonobjs[y].mobile+ "</a>";
	}
}
</script>
</head>
<!-- js代码通过webView调用其插件中的java代码 -->
<body onload="javascript:contact.getContacts()">
   <table border="0" width="100%" id="personTable" cellspacing="0">
		<tr bgcolor="00FFCC">
			<td width="20%">编号</td><td width="40%" align="center">姓名</td><td align="center">电话</td>
		</tr>
	</table>
	<img src="images/img.png" >
	<a href="javascript:window.location.reload()">刷新</a>
	<a href="javascript:contact.startAcivity()">跳转</a>
</body>
</html>
```
2. 我们需要一个类来加载html页面以及javascript的调用
MainActivity的代码如下：
```  
import org.json.JSONException;
import org.json.JSONObject;
import com.chinasofti.domain.Contact;
import com.chinasofti.service.ContactService;
import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;
import android.webkit.WebView;
public class MainActivity extends Activity {
	private static final String TAG = "MainActivity";
	private ContactService contactService;
	private WebView webView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		contactService = new ContactService();
		webView = (WebView) this.findViewById(R.id.webView);
		webView.getSettings().setJavaScriptEnabled(true);
		// 设置javaScript可用
		// window.open()
		webView.addJavascriptInterface(new ContactPlugin(), "contact");
		webView.loadUrl("file:///android_asset/index.html");
		// webView.loadUrl("http://192.168.1.100:8080/videoweb/index.html");
	}
	private final class ContactPlugin {
		public void getContacts() {
			List<Contact> contacts = contactService.getContacts();
			try {
				JSONArray array = new JSONArray();
				for (Contact contact : contacts) {
					JSONObject item = new JSONObject();
					item.put("id", contact.getId());
					item.put("name", contact.getName());
					item.put("mobile", contact.getMobile());
					array.put(item);
				}
				String json = array.toString();
				Log.i(TAG, json);
				webView.loadUrl("javascript:show('" + json + "')");
			} catch (JSONException e) {
				e.printStackTrace();
			}
		}
		public void call(String mobile) {
			Intent intent = new Intent(Intent.ACTION_CALL, Uri.parse("tel:
					+ mobile));
			startActivity(intent);
		}
		public void startAcivity() {
			Intent intent = new Intent(MainActivity.this, NewActivity.class);
			startActivity(intent);
		}
	}
}
```
在html页面中，我们可以点击超链接跳转到android中的Activity去，我们新建一个NewActivity
NewActivity代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
public class NewActivity extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.msg);
	}
}
```
布局文件中
main.xml的代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android
    android:layout_width="fill_parent
    android:layout_height="fill_parent
    android:orientation="vertical" >
    <WebView
        android:id="@+id/webView
        android:layout_width="fill_parent
        android:layout_height="fill_parent" />
</LinearLayout>
```
msg.xml代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <EditText
        android:id="@+id/editText1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/input" >
    </EditText>
</LinearLayout>
```
业务逻辑类 ContactService的代码如下：
```  
import java.util.ArrayList;
import java.util.List;
public class ContactService {
	public List<Contact> getContacts() {
		List<Contact> contacts = new ArrayList<Contact>();
		contacts.add(new Contact(34, "张三", "1398320333"));
		contacts.add(new Contact(39, "李四", "1332432444"));
		contacts.add(new Contact(67, "王五", "1300320333"));
		return contacts;
	}
}
```
用户实体类的代码如下：
```  
public class Contact {
	private Integer id;
	private String name;
	private String mobile;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getMobile() {
		return mobile;
	}
	public void setMobile(String mobile) {
		this.mobile = mobile;
	}
	public Contact(Integer id, String name, String mobile) {
		super();
		this.id = id;
		this.name = name;
		this.mobile = mobile;
	}
}
```
整个项目的布局如下图所示：
![img](P)  
项目的运行效果：
![img](P)  