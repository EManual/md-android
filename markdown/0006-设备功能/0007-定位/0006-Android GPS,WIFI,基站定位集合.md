集合了gps、wifi、基站定位。
其中GPS定位首先是GpsTask类异步返回GPS经纬度信息
```  
this.context = context;
GpsTask gpstask = new GpsTask(GpsActivity.this, new GpsTaskCallBack() {
	@Override
	public void gpsConnectedTimeOut() {
		gps_tip.setText("获取GPS超时了");
	}

	@Override
	public void gpsConnected(GpsData gpsdata) {
		do_gps(gpsdata);
	}
}, 3000);
gpstask.execute();
```
其中3000是设置获取gps数据timeout时间。GpsTask是根据
```  
locationManager.getLastKnownLocation(LocationManager.NETWORK_PROVIDER);
```
获取最后一次GPS信息，如果返回为Null则是根据监听获取Location信息发生改变时返回的GPS信息。因为手机获取GPS信息时间比较长，所以这个类实际使用时可能还存在一些BUG。
IAddressTask封装了获取地理位置的方法，具体代码如下：
```  
import java.io.BufferedReader;
import java.io.InputStreamReader;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.json.JSONArray;
import org.json.JSONObject;
import android.app.Activity;
import android.content.Context;
import android.net.wifi.WifiManager;
import android.telephony.TelephonyManager;
import android.telephony.gsm.GsmCellLocation;
public abstract class IAddressTask {
	protected Activity context;
	public IAddressTask(Activity context) {
		this.context = context;
	}
	public abstract HttpResponse execute(JSONObject params) throws Exception;
	public MLocation doWifiPost() throws Exception {
		return transResponse(execute(doWifi()));
	}
	public MLocation doApnPost() throws Exception {
		return transResponse(execute(doApn()));
	}
	public MLocation doGpsPost(double lat, double lng) throws Exception {
		return transResponse(execute(doGps(lat, lng)));
	}
	private MLocation transResponse(HttpResponse response) {
		MLocation location = null;
		if (response.getStatusLine().getStatusCode() == 200) {
			location = new MLocation();
			HttpEntity entity = response.getEntity();
			BufferedReader br;
			try {
				br = new BufferedReader(new InputStreamReader(
						entity.getContent()));
				StringBuffer sb = new StringBuffer();
				String result = br.readLine();
				while (result != null) {
					sb.append(result);
					result = br.readLine();
				}
				JSONObject json = new JSONObject(sb.toString());
				JSONObject lca = json.getJSONObject("location");
				location.Access_token = json.getString("access_token");
				if (lca != null) {
					if (lca.has("accuracy"))
						location.Accuracy = lca.getString("accuracy");
					if (lca.has("longitude"))
						location.Latitude = lca.getDouble("longitude");
					if (lca.has("latitude"))
						location.Longitude = lca.getDouble("latitude");
					if (lca.has("address")) {
						JSONObject address = lca.getJSONObject("address");
						if (address != null) {
							if (address.has("region"))
								location.Region = address.getString("region");
							if (address.has("street_number"))
								location.Street_number = address
										.getString("street_number");
							if (address.has("country_code"))
								location.Country_code = address
										.getString("country_code");
							if (address.has("street"))
								location.Street = address.getString("street");
							if (address.has("city"))
								location.City = address.getString("city");
							if (address.has("country"))
								location.Country = address.getString("country");
						}
					}
				}
			} catch (Exception e) {
				e.printStackTrace();
				location = null;
			}
		}
		return location;
	}
	private JSONObject doGps(double lat, double lng) throws Exception {
		JSONObject holder = new JSONObject();
		holder.put("version", "1.1.0");
		holder.put("host", "maps.google.com");
		holder.put("address_language", "zh_CN");
		holder.put("request_address", true);
		JSONObject data = new JSONObject();
		data.put("latitude", lat);
		data.put("longitude", lng);
		holder.put("location", data);
		return holder;
	}
	private JSONObject doApn() throws Exception {
		JSONObject holder = new JSONObject();
		holder.put("version", "1.1.0");
		holder.put("host", "maps.google.com");
		holder.put("address_language", "zh_CN");
		holder.put("request_address", true);
		TelephonyManager tm = (TelephonyManager) context
				.getSystemService(Context.TELEPHONY_SERVICE);
		GsmCellLocation gcl = (GsmCellLocation) tm.getCellLocation();
		int cid = gcl.getCid();
		int lac = gcl.getLac();
		int mcc = Integer.valueOf(tm.getNetworkOperator().substring(0, 3));
		int mnc = Integer.valueOf(tm.getNetworkOperator().substring(3, 5));
		JSONArray array = new JSONArray();
		JSONObject data = new JSONObject();
		data.put("cell_id", cid);
		data.put("location_area_code", lac);
		data.put("mobile_country_code", mcc);
		data.put("mobile_network_code", mnc);
		array.put(data);
		holder.put("cell_towers", array);
		return holder;
	}
	private JSONObject doWifi() throws Exception {
		JSONObject holder = new JSONObject();
		holder.put("version", "1.1.0");
		holder.put("host", "maps.google.com");
		holder.put("address_language", "zh_CN");
		holder.put("request_address", true);
		WifiManager wifiManager = (WifiManager) context
				.getSystemService(Context.WIFI_SERVICE);
		if (wifiManager.getConnectionInfo().getBSSID() == null) {
			throw new RuntimeException("bssid is null");
		}
		JSONArray array = new JSONArray();
		JSONObject data = new JSONObject();
		data.put("mac_address", wifiManager.getConnectionInfo().getBSSID());
		data.put("signal_strength", 8);
		data.put("age", 0);
		array.put(data);
		holder.put("wifi_towers", array);
		return holder;
	}
	public static class MLocation {
		public String Access_token;
		public double Latitude;
		public double Longitude;
		public String Accuracy;
		public String Region;
		public String Street_number;
		public String Country_code;
		public String Street;
		public String City;
		public String Country;
		@Override
		public String toString() {
			StringBuffer buffer = new StringBuffer();
			buffer.append("Access_token:" + Access_token + "\n");
			buffer.append("Region:" + Region + "\n");
			buffer.append("Accuracy:" + Accuracy + "\n");
			buffer.append("Latitude:" + Latitude + "\n");
			buffer.append("Longitude:" + Longitude + "\n");
			buffer.append("Country_code:" + Country_code + "\n");
			buffer.append("Country:" + Country + "\n");
			buffer.append("City:" + City + "\n");
			buffer.append("Street:" + Street + "\n");
			buffer.append("Street_number:" + Street_number + "\n");
			return buffer.toString();
		}
	}
}
```
GpsActivity.java
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.os.AsyncTask;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.TextView;
import device.gps.IAddressTask.MLocation;
public class GpsActivity extends Activity implements OnClickListener {
	private TextView gps_tip = null;
	private AlertDialog dialog = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		gps_tip = (TextView) findViewById(R.id.gps_tip);
		findViewById(R.id.do_gps).setOnClickListener(GpsActivity.this);
		findViewById(R.id.do_apn).setOnClickListener(GpsActivity.this);
		findViewById(R.id.do_wifi).setOnClickListener(GpsActivity.this);
		dialog = new ProgressDialog(GpsActivity.this);
		dialog.setTitle("请稍等...");
		dialog.setMessage("正在定位...");
	}
	@SuppressWarnings("unchecked")
	@Override
	public void onClick(View v) {
		gps_tip.setText("");
		switch (v.getId()) {
		case R.id.do_apn:
			do_apn();
			break;
		case R.id.do_gps:
			GpsTask gpstask = new GpsTask(GpsActivity.this,
					new GpsTaskCallBack() {
						@Override
						public void gpsConnectedTimeOut() {
							gps_tip.setText("获取GPS超时了");
						}

						@Override
						public void gpsConnected(GpsData gpsdata) {
							do_gps(gpsdata);
						}
					}, 3000);
			gpstask.execute();
			break;
		case R.id.do_wifi:
			do_wifi();
			break;
		}
	}
	private void do_apn() {
		new AsyncTask<Void, Void, String>() {
			@Override
			protected String doInBackground(Void... params) {
				MLocation location = null;
				try {
					location = new AddressTask(GpsActivity.this,
							AddressTask.DO_APN).doApnPost();
				} catch (Exception e) {
					e.printStackTrace();
				}
				if (location == null)
					return null;
				return location.toString();
			}
			@Override
			protected void onPreExecute() {
				dialog.show();
				super.onPreExecute();
			}
			@Override
			protected void onPostExecute(String result) {
				if (result == null) {
					gps_tip.setText("基站定位失败了...");
				} else {
					gps_tip.setText(result);
				}
				dialog.dismiss();
				super.onPostExecute(result);
			}
		}.execute();
	}
	private void do_gps(final GpsData gpsdata) {
		new AsyncTask<Void, Void, String>() {
			@Override
			protected String doInBackground(Void... params) {
				MLocation location = null;
				try {
					Log.i("do_gpspost", "经纬度：" + gpsdata.getLatitude() + "----
							+ gpsdata.getLongitude());
					location = new AddressTask(GpsActivity.this,
							AddressTask.DO_GPS).doGpsPost(
							gpsdata.getLatitude(), gpsdata.getLongitude());
				} catch (Exception e) {
					e.printStackTrace();
				}
				if (location == null)
					return "GPS信息获取错误";
				return location.toString();
			}
			@Override
			protected void onPreExecute() {
				dialog.show();
				super.onPreExecute();
			}
			@Override
			protected void onPostExecute(String result) {
				gps_tip.setText(result);
				dialog.dismiss();
				super.onPostExecute(result);
			}
		}.execute();
	}
	private void do_wifi() {
		new AsyncTask<Void, Void, String>() {
			@Override
			protected String doInBackground(Void... params) {
				MLocation location = null;
				try {
					location = new AddressTask(GpsActivity.this,
							AddressTask.DO_WIFI).doWifiPost();
				} catch (Exception e) {
					e.printStackTrace();
				}
				if (location == null)
					return null;
				return location.toString();
			}
			@Override
			protected void onPreExecute() {
				dialog.show();
				super.onPreExecute();
			}
			@Override
			protected void onPostExecute(String result) {
				if (result != null) {
					gps_tip.setText(result);
				} else {
					gps_tip.setText("WIFI定位失败了...");
				}
				dialog.dismiss();
				super.onPostExecute(result);
			}
		}.execute();
	}
}
```
AddressTask.java
```  
import org.apache.http.HttpHost;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.conn.params.ConnRouteParams;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.params.HttpConnectionParams;
import org.json.JSONObject;

import android.app.Activity;
import android.database.Cursor;
import android.net.Uri;
public class AddressTask extends IAddressTask {
	public static final int DO_APN = 0;
	public static final int DO_WIFI = 1;
	public static final int DO_GPS = 2;
	private int postType = -1;
	public AddressTask(Activity context, int postType) {
		super(context);
		this.postType = postType;
	}
	@Override
	public HttpResponse execute(JSONObject params) throws Exception {
		HttpClient httpClient = new DefaultHttpClient();
		HttpConnectionParams.setConnectionTimeout(httpClient.getParams(),
				20 * 1000);
		HttpConnectionParams.setSoTimeout(httpClient.getParams(), 20 * 1000);
		HttpPost post = new HttpPost("http://74.125.71.147/loc/json"); // 设置代理
		if (postType == DO_APN) {
			Uri uri = Uri.parse("content://telephony/carriers/preferapn"); // 获取当前正在使用的APN接入点
			Cursor mCursor = context.getContentResolver().query(uri, null,
					null, null, null);
			if (mCursor != null) {
				if (mCursor.moveToFirst()) {
					String proxyStr = mCursor.getString(mCursor
							.getColumnIndex("proxy"));
					if (proxyStr != null && proxyStr.trim().length() > 0) {
						HttpHost proxy = new HttpHost(proxyStr, 80);
						httpClient.getParams().setParameter(
								ConnRouteParams.DEFAULT_PROXY, proxy);
					}
				}
			}
		}
		StringEntity se = new StringEntity(params.toString());
		post.setEntity(se);
		HttpResponse response = httpClient.execute(post);
		return response;
	}
}
```