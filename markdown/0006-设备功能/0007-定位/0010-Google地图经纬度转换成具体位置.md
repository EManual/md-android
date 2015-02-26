代码如下：
```  
import java.io.BufferedReader;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLConnection;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;
public class LocationDemoActivity extends Activity implements OnClickListener {
	private final double lat = 31.9777;
	private final double lng = 118.767481;
	private TextView addressTView;
	private Button parseBtn;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		parseBtn = (Button) findViewById(R.id.address_parse);
		addressTView = (TextView) findViewById(R.id.address);
		parseBtn.setOnClickListener(this);
	}
	 /**
	  * 由经纬度获得地址
	  * 
	  * @param latitude
	  *            纬度
	  * @param longitude
	  *            经度
	  * @return
	  */
	private static JSONObject geocodeAddr(double lat, double lng) {
		String urlString = "http://ditu.google.com/maps/geo?q=+" + lat + ",
				+ lng + "&output=json&oe=utf8&hl=zh-CN&sensor=false";
		// String urlString =
		// "http://maps.google.com/maps/api/geocode/json?latlng="+lat+","+lng+"&language=zh_CN&sensor=false";
		StringBuilder sTotalString = new StringBuilder();
		try {
			URL url = new URL(urlString);
			URLConnection connection = url.openConnection();
			HttpURLConnection httpConnection = (HttpURLConnection) connection;

			InputStream urlStream = httpConnection.getInputStream();
			BufferedReader bufferedReader = new BufferedReader(
					new InputStreamReader(urlStream));

			String sCurrentLine = "";
			while ((sCurrentLine = bufferedReader.readLine()) != null) {
				sTotalString.append(sCurrentLine);
			}
			bufferedReader.close();
			httpConnection.disconnect(); // 关闭http连接
		} catch (Exception e1) {
			e1.printStackTrace();
		}
		JSONObject jsonObject = new JSONObject();
		try {
			jsonObject = new JSONObject(sTotalString.toString());
		} catch (JSONException e) {
			e.printStackTrace();
		}
		return jsonObject;
	}
	private static String getAddressByLatLng(double lat, double lng) {
		String address = null;
		JSONObject jsonObject = geocodeAddr(lat, lng);
		try {
			JSONArray placemarks = jsonObject.getJSONArray("Placemark");
			JSONObject place = placemarks.getJSONObject(0);
			address = place.getString("address");
		} catch (Exception e) {
			e.printStackTrace();
		}
		return address;
	}
	@Override
	public void onClick(View v) {
		switch (v.getId()) {
		case R.id.address_parse:
			String address = getAddressByLatLng(lat, lng);
			addressTView.setText(address);
			break;

		default:
			break;
		}
	}
}
```