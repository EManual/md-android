MediaFactory.java
```  
import android.content.ContentResolver;
import android.content.ContentUris;
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.net.Uri;
import android.util.Log;
public class MediaFactory {
	private final static String TAG = "GrevianMedia";
	public static Media getMediaByUPC(Context mContext, String UPC)
			throws LookupException {
		Media mMedia = null;
		ContentResolver cr = mContext.getContentResolver();
		// Try to lookup the content with the CR first
		Uri myItem = ContentUris.withAppendedId(Media.CONTENT_URI, Long.valueOf(UPC));
		Cursor cur = cr.query(myItem, null, null, null, null);
		if (cur.getCount() > 0) {
			cur.moveToFirst();
			mMedia = new Media(cur, cr);
		}
		cur.close();
		if (mMedia != null)
			return mMedia;
		// Look it up online, then insert it into the CR if we successfully find it
		Log.w(TAG, "ContentResolver Miss for UPC: " + UPC);
		Log.i(TAG, "Looking up UPC Online...");
		String Title = UPCDataSource.getUPCText(UPC).trim();
		Log.i(TAG, "UPC Lookup Result: " + Title);
		if (Title == "")
			throw new LookupException("Could not find Title Anywhere!");
		ContentValues mVals = new ContentValues();
		mVals.put(Media.TITLE, Title);
		mVals.put(Media.BARCODE, UPC);
		mVals.put(Media.OWNED, 0);
		mVals.put(Media.LOANED, "");
		cr.insert(Media.CONTENT_URI, mVals);
		// Ok, Mobile platforms probably don't like recursion, but this really
		// is the most graceful way to handle things...
		return MediaFactory.getMediaByUPC(mContext, UPC);
	}
}
```
MediaLibrary.java
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.AdapterView;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;
public class MediaLibrary extends Activity {
	public static final String AUTHORITY = "grevian.MediaLibrary";
	private final static String TAG = "GrevianMedia";
	private TextSearchAdapter mSearchAdapter;

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Log.i(TAG, "Application Started.");
		// Setup the searching field
		EditText searchText = (EditText) findViewById(R.id.SearchText);
		mSearchAdapter = new TextSearchAdapter(
				((ListView) findViewById(R.id.ResultList)), searchText,
				this.getContentResolver());
		if (savedInstanceState != null
				&& savedInstanceState.containsKey("searchText")) {
			searchText.setText(savedInstanceState.getString("searchText"));
			mSearchAdapter.update();
		}
		// Setup the scanner
		Button mButton = (Button) findViewById(R.id.ScanButton);
		mButton.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				Intent intent = new Intent(
						"com.google.zxing.client.android.SCAN");
				intent.putExtra("SCAN_MODE", "ONE_D_MODE");
				startActivityForResult(intent, 0);
			}
		});
		// Set up the list to display item details when an item is selected
		ListView mList = (ListView) findViewById(R.id.ResultList);
		mList.setOnItemClickListener(new ListView.OnItemClickListener() {
			public void onItemClick(AdapterView<?> ListContents, View mView,
					int position, long arg3) {
				Media itemDetails = (Media) ListContents
						.getItemAtPosition(position);
				Intent i = new Intent(MediaLibrary.this,
						grevian.MediaLibrary.ItemFoundActivity.class);
				i.putExtra("UPC", itemDetails.getUPC());
				startActivity(i);
			}
		});

	}
	public void onActivityResult(int requestCode, int resultCode, Intent intent) {
		Log.v(TAG, "onActivityResult called in ScanHandler");
		if (requestCode == 0) {
			if (resultCode == RESULT_OK) {
				String contents = intent.getStringExtra("SCAN_RESULT");
				Intent i = new Intent(MediaLibrary.this, grevian.MediaLibrary.ItemFoundActivity.class);
				i.putExtra("UPC", contents);
				startActivity(i);
			} else if (resultCode == RESULT_CANCELED) {
				Log.i(TAG, "Scan Cancelled.");
			}
		}
	}
}
```