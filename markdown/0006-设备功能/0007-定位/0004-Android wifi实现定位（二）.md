java代码：
```  
[Tags]/*WifiInfoManager.java 可获取wifi的信息，目前我只取了当前连接的wifi，没有获取所有能扫描到的wifi信息。 */
import java.util.ArrayList;
import android.content.Context;
import android.net.wifi.WifiManager;
public class WifiInfoManager {
	WifiManager wm;
	public WifiInfoManager() {
	}
	public ArrayList getWifiInfo(Context context) {
		ArrayList<WifiInfo> wifi = new ArrayList();
		wm = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
		WifiInfo info = new WifiInfo();
		info.mac = wm.getConnectionInfo().getBSSID();
		wifi.add(info);
		return wifi;
	}
	// 调用google gears的方法，该方法调用gears来获取经纬度
	private Location callGear(ArrayList<WifiInfo> wifi,
			ArrayList<CellIDInfo> cellID) {
		if (cellID == null)
			return null;
		DefaultHttpClient client = new DefaultHttpClient();
		HttpPost post = new HttpPost("http://www.google.com/loc/json");
		JSONObject holder = new JSONObject();
		try {
			holder.put("version", "1.1.0");
			holder.put("host", "maps.google.com");
			holder.put("home_mobile_country_code",
					cellID.get(0).mobileCountryCode);
			holder.put("home_mobile_network_code",
					cellID.get(0).mobileNetworkCode);
			holder.put("radio_type", cellID.get(0).radioType);
			holder.put("request_address", true);
			if ("460".equals(cellID.get(0).mobileCountryCode))
				holder.put("address_language", "zh_CN");
			else
				holder.put("address_language", "en_US");
			JSONObject data, current_data;
			JSONArray array = new JSONArray();
			current_data = new JSONObject();
			current_data.put("cell_id", cellID.get(0).cellId);
			current_data.put("mobile_country_code",
					cellID.get(0).mobileCountryCode);
			current_data.put("mobile_network_code",
					cellID.get(0).mobileNetworkCode);
			current_data.put("age", 0);
			array.put(current_data);
			if (cellID.size() > 2) {
				for (int i = 1; i < cellID.size(); i++) {
					data = new JSONObject();
					data.put("cell_id", cellID.get(i).cellId);
					data.put("location_area_code",
							cellID.get(0).locationAreaCode);
					data.put("mobile_country_code",
							cellID.get(0).mobileCountryCode);
					data.put("mobile_network_code",
							cellID.get(0).mobileNetworkCode);
					data.put("age", 0);
					array.put(data);
				}
			}
			holder.put("cell_towers", array);
			if (wifi.get(0).mac != null) {
				data = new JSONObject();
				data.put("mac_address", wifi.get(0).mac);
				data.put("signal_strength", 8);
				data.put("age", 0);
				array = new JSONArray();
				array.put(data);
				holder.put("wifi_towers", array);
			}
			StringEntity se = new StringEntity(holder.toString());
			Log.e("Location send", holder.toString());
			post.setEntity(se);
			HttpResponse resp = client.execute(post);
			HttpEntity entity = resp.getEntity();
			BufferedReader br = new BufferedReader(new InputStreamReader(
					entity.getContent()));
			StringBuffer sb = new StringBuffer();
			String result = br.readLine();
			while (result != null) {
				Log.e("Locaiton reseive", result);
				sb.append(result);
				result = br.readLine();
			}
			data = new JSONObject(sb.toString());
			data = (JSONObject) data.get("location");
			Location loc = new Location(LocationManager.NETWORK_PROVIDER);
			loc.setLatitude((Double) data.get("latitude"));
			loc.setLongitude((Double) data.get("longitude"));
			loc.setAccuracy(Float.parseFloat(data.get("accuracy").toString()));
			loc.setTime(AppUtil.getUTCTime());
			return loc;
		} catch (JSONException e) {
			return null;
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		} catch (ClientProtocolException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
		return null;
	}
}
```