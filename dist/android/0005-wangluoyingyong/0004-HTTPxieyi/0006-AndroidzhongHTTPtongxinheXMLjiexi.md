我们要记住的就是HTTP通信与服务端做HTTP通信，分别以GET方式和POST方式做演示。XML解析可以用两种方式解析XML，分别是DOM方式和SAX方式。异步消息处理-通过Handler实现异步消息处理，以一个自定义的异步下载类来说明Handler的用法。现在我们大家应该明白HTTP和xml了吧，那我们就来看看代码是怎么写的吧，代码中有注释。下面的代码很长大家可要仔细看。
```  
import java.io.BufferedInputStream;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLConnection;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.protocol.HTTP;
import org.apache.http.util.ByteArrayBuffer;
import org.apache.http.util.EncodingUtils;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.NodeList;
import org.xml.sax.InputSource;
import org.xml.sax.XMLReader;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
public class Main extends Activity {
	private TextView textView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		textView = (TextView) this.findViewById(R.id.textView);
		Button btn1 = (Button) this.findViewById(R.id.btn1);
		btn1.setText("http get demo");
		btn1.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				httpGetDemo();
			}
		});
		Button btn2 = (Button) this.findViewById(R.id.btn2);
		btn2.setText("http post demo");
		btn2.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				httpPostDemo();
			}
		});
		Button btn3 = (Button) this.findViewById(R.id.btn3);
		// DOM - Document Object Model
		btn3.setText("DOM 解析 XML");
		btn3.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				DOMDemo();
			}
		});
		Button btn4 = (Button) this.findViewById(R.id.btn4);
		// SAX - Simple API for XML
		btn4.setText("SAX 解析 XML");
		btn4.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				SAXDemo();
			}
		});
	}
	// Android 调用 http 协议的 get 方法
	// 本例：以 http 协议的 get 方法获取远程页面响应的内容
	private void httpGetDemo() {
		try {
			// 模拟器测试时，请使用外网地址
			URL url = new URL("http://xxx.xxx.xxx");
			URLConnection con = url.openConnection();
			String result = "http status code:
					+ ((HttpURLConnection) con).getResponseCode() + "\n";
			// HttpURLConnection.HTTP_OK
			InputStream is = con.getInputStream();
			BufferedInputStream bis = new BufferedInputStream(is);
			ByteArrayBuffer bab = new ByteArrayBuffer(32);
			int current = 0;
			while ((current = bis.read()) != -1) {
				bab.append((byte) current);
			}
			result += EncodingUtils.getString(bab.toByteArray(), HTTP.UTF_8);
			bis.close();
			is.close();
			textView.setText(result);
		} catch (Exception e) {
			textView.setText(e.toString());
		}
	}
	// Android 调用 http 协议的 post 方法
	// 本例：以 http 协议的 post 方法向远程页面传递参数，并获取其响应的内容
	private void httpPostDemo() {
		try {
			// 模拟器测试时，请使用外网地址
			String url = "http://5billion.com.cn/post.php";
			Map<String, String> data = new HashMap<String, String>();
			data.put("name", "webabcd");
			data.put("salary", "100");
			DefaultHttpClient httpClient = new DefaultHttpClient();
			HttpPost httpPost = new HttpPost(url);
			ArrayList<BasicNameValuePair> postData = new ArrayList<BasicNameValuePair>();
			for (Map.Entry<String, String> m : data.entrySet()) {
				postData.add(new BasicNameValuePair(m.getKey(), m.getValue()));
			}
			UrlEncodedFormEntity entity = new UrlEncodedFormEntity(postData,
					HTTP.UTF_8);
			httpPost.setEntity(entity);
			HttpResponse response = httpClient.execute(httpPost);
			String result = "http status code:
					+ response.getStatusLine().getStatusCode() + "\n";
			// HttpURLConnection.HTTP_OK
			HttpEntity httpEntity = response.getEntity();
			InputStream is = httpEntity.getContent();
			result += convertStreamToString(is);
			textView.setText(result);
		} catch (Exception e) {
			textView.setText(e.toString());
		}
	}
	// 以 DOM 方式解析 XML（xml 数据详见 res/raw/employee.xml）
	private void DOMDemo() {
		try {
			DocumentBuilderFactory docFactory = DocumentBuilderFactory
					.newInstance();
			DocumentBuilder docBuilder = docFactory.newDocumentBuilder();
			Document doc = docBuilder.parse(this.getResources()
					.openRawResource(R.raw.employee));
			Element rootElement = doc.getDocumentElement();
			NodeList employeeNodeList = rootElement
					.getElementsByTagName("employee");

			textView.setText("DOMDemo" + "\n");
			String title = rootElement.getElementsByTagName("title").item(0)
					.getFirstChild().getNodeValue();
			textView.append(title);
			for (int i = 0; i < employeeNodeList.getLength(); i++) {
				Element employeeElement = ((Element) employeeNodeList.item(i));
				String name = employeeElement.getAttribute("name");
				String salary = employeeElement.getElementsByTagName("salary")
						.item(0).getFirstChild().getNodeValue();
				String dateOfBirth = employeeElement
						.getElementsByTagName("dateOfBirth").item(0)
						.getFirstChild().getNodeValue();
				textView.append("\nname: " + name + " salary: " + salary
						+ " dateOfBirth: " + dateOfBirth);
			}
		} catch (Exception e) {
			textView.setText(e.toString());
		}
	}
	// 以 SAX 方式解析 XML（xml 数据详见 res/raw/employee.xml）
	// SAX 解析器的实现详见 MySAXHandler.java
	private void SAXDemo() {
		try {
			SAXParserFactory saxFactory = SAXParserFactory.newInstance();
			SAXParser parser = saxFactory.newSAXParser();
			XMLReader reader = parser.getXMLReader();

			MySAXHandler handler = new MySAXHandler();
			reader.setContentHandler(handler);
			reader.parse(new InputSource(this.getResources().openRawResource(
					R.raw.employee)));
			String result = handler.getResult();
			textView.setText("SAXDemo" + "\n");
			textView.append(result);
		} catch (Exception e) {
			textView.setText(e.toString());
		}
	}
	// 辅助方法，用于把流转换为字符串
	private String convertStreamToString(InputStream is) {
		BufferedReader reader = new BufferedReader(new InputStreamReader(is));
		StringBuilder sb = new StringBuilder();

		String line = null;
		try {
			while ((line = reader.readLine()) != null) {
				sb.append(line + "\n");
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				is.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return sb.toString();
	}
}
```