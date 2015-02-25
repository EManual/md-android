movieDetailsDatasource.java
```  
import java.util.HashMap;
import org.xmlrpc.android.XMLRPCClient;
import android.util.Log;
public class movieDetailsDatasource {
	@SuppressWarnings("unchecked")
	public static String getDetailsByTitle(String title) {
		String text = "";
		try {
			XMLRPCClient client = new XMLRPCClient("http://api.opensubtitles.org/xml-rpc");
			HashMap<String, String> loginResults = (HashMap<String, String>) client
					.call("LogIn", "", "", "CA", "GrevianMedia 0.1");
			String sessionToken = loginResults.get("token");
			String[][] args = { { "query", title } };
			HashMap<String, String> results = (HashMap<String, String>) client
					.call("SearchSubtitles", sessionToken, args);
			if (results.size() > 0
					&& results.get("message").equalsIgnoreCase(
							"Database entry found")) {
				// TODO: Do a query to get imdb details and shuffle them into an
				// appropriate data structure
				text = results.get("description");
			}
			client.call("LogOut", sessionToken);
		} catch (Exception e) {
			Log.e("GrevianMedia", e.getMessage());
			return "";
		}
		return text;
	}
}
```
TextSearchAdapter.java
```  
import android.content.ContentResolver;
import android.database.Cursor;
import android.graphics.Color;
import android.net.Uri;
import android.util.Log;
import android.view.KeyEvent;
import android.view.View;
import android.view.ViewGroup;
import android.view.View.OnKeyListener;
import android.widget.BaseAdapter;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.TextView;
public class TextSearchAdapter extends BaseAdapter implements OnKeyListener {
	private final static String TAG = "GrevianMedia";
	private ListView _list;
	private EditText _text;
	private Uri _searchUri;
	private ContentResolver cr;
	private Cursor _cursor;
	public TextSearchAdapter(ListView mList, EditText mText,
			ContentResolver contentResolver) {
		cr = contentResolver;
		_list = mList;
		_text = mText;
		_searchUri = Media.SEARCH_URI;
		Uri currentUri = _searchUri.buildUpon().appendPath(_text.getText().toString()).build();
		_cursor = cr.query(currentUri, null, null, null, null);
		_text.setOnKeyListener(this);
		_list.setAdapter(this);
	}
	@Override
	public int getCount() {
		return _cursor.getCount();
	}
	@Override
	public Object getItem(int position) {
		_cursor.moveToPosition(position);
		return new Media(_cursor, cr);
	}
	@Override
	public long getItemId(int position) {
		return position;
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		// if the existing view is null, create it as a textview
		if (convertView == null)
			convertView = new TextView(parent.getContext());
		TextView mText = (TextView) convertView;
		Media mMedia = (Media) getItem(position);
		mText.setText(mMedia.getTitle());
		if (mMedia.getOwned() > 0)
			mText.setTextColor(Color.GREEN);
		else
			mText.setTextColor(Color.RED);
		return mText;
	}
	public boolean update() {
		// Time and execute the search query
		long sTime = System.currentTimeMillis();
		_cursor.close(); // Close the old cursor
		Uri currentUri = _searchUri.buildUpon()
				.appendPath(_text.getText().toString()).build();
		_cursor = cr.query(currentUri, null, null, null, null);
		// Probably not needed, but doesn't hurt
		_cursor.moveToFirst();
		long fTime = System.currentTimeMillis();
		Log.i(TAG, "Search for term '" + _text.getText().toString()
				+ "' returned " + _cursor.getCount() + " results in
				+ (fTime - sTime) + "ms");
		this.notifyDataSetChanged();
		return true;
	}
	@Override
	public boolean onKey(View v, int keyCode, KeyEvent event) {
		update();
		return false;
	}
}
```