最近初步在搞android怎么去跟webservice的连接，调用参数返回。在这里发下我运行的源码。已测试，已成功！贴上我的代码！
```  
import java.io.IOException;
import org.xmlpull.v1.XmlPullParserException;
import android.R;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
public class LQserviceActivity extends Activity {
	[Tags]/** Called when the activity is first created. */
	private Button button;
	private EditText editText;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		button = (Button) findViewById(R.id.button);
		editText = (EditText) findViewById(R.id.edit);
		button.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				String show = editText.getText().toString();
				String showMessage = "";
				try {
					showMessage = getPhoneInfo(show);
					editText.setText(showMessage);
				} catch (XmlPullParserException e) {
				} catch (IOException e) {
					e.printStackTrace();
				}
			}

		});
	}
	public String getPhoneInfo(String phoneName) throws IOException,
			XmlPullParserException {
		// 获取URL
		String URL = "http://61.143.165.38/wlss.asmx";
		// 返回的查询结果
		String result = null;
		// 调用webservice接口的命名空间
		String namespace = "http://61.143.165.38/";
		// 调用的方法名
		String methodName = "HelloWorld";
		// webservice中SOAPAction的地址
		String SOAP_ACTION = "http://www.1008656.com/HelloWorld";
		// 获得返回请求对象
		SoapObject request = new SoapObject(namespace, methodName);
		// 设置需要返回请求对象方法的参数
		// request.addProperty("moblieCode",phoneName);
		// request.addProperty("userId","");
		// 设置soap的版本
		SoapSerializationEnvelope envelope = new SoapSerializationEnvelope(
				SoapEnvelope.VER11);
		// 设置是否调用的是dotNet开发的,这里如果设置为TRUE,那么在服务器端将获取不到参数值(如:将这些数据插入到数据库中的话)
		envelope.dotNet = true;
		envelope.bodyOut = request;
		AndroidHttpTransport hts = new AndroidHttpTransport(URL);
		// web service请求
		hts.call(SOAP_ACTION, envelope);
		// hts.call(null,envelope);
		// 得到返回结果
		Object o = envelope.getResponse();
		result = o.toString();
		return result;
	}
}
```
以下是main.xml源码
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!-- android:text="webservice的调用" -->
    <Button
        android:id="@+id/button"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="点击" />
    <EditText
        android:id="@+id/edit"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```