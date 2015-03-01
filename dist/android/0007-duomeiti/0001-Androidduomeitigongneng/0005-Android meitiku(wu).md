UPCDataSource.java
```  
import java.util.HashMap;
import org.xmlrpc.android.XMLRPCClient;
import android.util.Log;
public class UPCDataSource {
	@SuppressWarnings("unchecked")
	public static String getUPCText(String upc) {
		String text = "";
		try {
			XMLRPCClient client = new XMLRPCClient(
					"http://www.upcdatabase.com/rpc");
			HashMap<String, String> results = (HashMap<String, String>) client
					.call("lookupUPC", upc);
			if (results.size() > 0
					&& results.get("message").equalsIgnoreCase(
							"Database entry found")) {
				text = results.get("description");
			}
		} catch (Exception e) {
			Log.e("GrevianMedia", e.getMessage());
			return "";
		}
		return text;
	}
}
```
UPCRESTSource.java
```  
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;
import org.json.JSONException;
import org.json.JSONObject;
import android.util.Log;
public class UPCRESTSource {
	private static String url = "";
	private static String convertStreamToString(InputStream is) {
		/*
		  * To convert the InputStream to String we use the
		  * BufferedReader.readLine() method. We iterate until the BufferedReader
		  * return null which means there's no more data to read. Each line will
		  * appended to a StringBuilder and returned as String.
		  */
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
	public static String getUPCText(String upc) {
		HttpClient httpclient = new DefaultHttpClient();
		HttpGet httpget = new HttpGet(url);
		HttpResponse response;
		String text = "";
		try {
			response = httpclient.execute(httpget);
			// Examine the response status
			Log.i("Praeda", response.getStatusLine().toString());
			// Get hold of the response entity
			HttpEntity entity = response.getEntity();
			if (entity != null) {
				// A Simple JSON Response Read
				InputStream instream = entity.getContent();
				String result = convertStreamToString(instream);
				Log.i("Praeda", result);
				// A Simple JSONObject Creation
				JSONObject json = new JSONObject(result);
				Log.i("Praeda", "<jsonobject>\n" + json.toString() + "\n</jsonobject>");
				text = json.getString("result");
				Log.i("Praeda", "<jsonobject>\n" + json.toString() + "\n</jsonobject>");
				// Closing the input stream will trigger connection release
				instream.close();
			}
		} catch (ClientProtocolException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} catch (JSONException e) {
			e.printStackTrace();
		}
		return text;
	}
}
```