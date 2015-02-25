今天和大家分享一下grevian.MediaLibrary中的一些有用的代码，希望大家能用上，不多说了，还是来看看代码吧。
ItemFoundActivity.java
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
public class ItemFoundActivity extends Activity {
	private Media mMedia;
	private Bundle extras;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.details);
		extras = getIntent().getExtras();
		try {
			mMedia = MediaFactory.getMediaByUPC(this.getBaseContext(),
					extras.getString("UPC"));
		} catch (LookupException e) {
			Toast toast = Toast.makeText(getApplicationContext(),
					e.getMessage(), Toast.LENGTH_LONG);
			toast.show();
			this.finish();
			return;
		}
		TextView titleText = (TextView) findViewById(R.id.TitleText);
		titleText.setText(String.valueOf(mMedia.getTitle()));
		final TextView ownedText = (TextView) findViewById(R.id.CopiesText);
		ownedText.setText(Integer.toString(mMedia.getOwned()));
		if (mMedia.getLoaned() != "") {
			TextView loanedText = (TextView) findViewById(R.id.LoanedText);
			loanedText.setText(mMedia.getLoaned());
		}
		// Set up the button to add copies
		Button mButton = (Button) findViewById(R.id.AddButton);
		mButton.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				mMedia.setOwned(mMedia.getOwned() + 1);
				mMedia.save();
				ownedText.setText(Integer.toString(mMedia.getOwned()));
			}
		});
	}
}
```
LookupException.java
```  
public class LookupException extends Exception {
	private static final long serialVersionUID = -8069511109138795672L;
	public LookupException(String Reason) {
		super(Reason);
	}
}
```
Media.java
```  
import android.content.ContentResolver;
import android.content.ContentValues;
import android.database.Cursor;
import android.net.Uri;
public class Media {
	public static final Uri CONTENT_URI = Uri.parse("content://" + MediaLibrary.AUTHORITY + "/media");
	public static final Uri SEARCH_URI = Uri.parse("content://" + MediaLibrary.AUTHORITY + "/search/");
	public static final String CONTENT_TYPE = "vnd.android.cursor.dir/grevian.MediaLibrary.Media";
	public static final String CONTENT_ITEM_TYPE = "vnd.android.cursor.item/grevian.MediaLibrary.Media";
	public static final String DEFAULT_SORT_ORDER = "lower(title) ASC";
	public static final String BARCODE = "barcode";
	public static final String TITLE = "title";
	public static final String OWNED = "owned";
	public static final String LOANED = "LOANED";
	private String Title;
	private String UPC;
	private int Owned;
	private String Loaned;
	private ContentResolver cr;
	public Media(Cursor c, ContentResolver contentResolver) {
		setTitle(c.getString(c.getColumnIndexOrThrow("title")));
		setUPC(c.getString(c.getColumnIndexOrThrow("barcode")));
		setOwned(c.getInt(c.getColumnIndexOrThrow("owned")));
		setLoaned(c.getString(c.getColumnIndexOrThrow("loaned")));
		cr = contentResolver;
	}
	public void setTitle(String title) {
		Title = title;
	}
	public String getTitle() {
		return Title;
	}
	public void setUPC(String uPC) {
		UPC = uPC;
	}
	public String getUPC() {
		return UPC;
	}
	public void setOwned(int owned) {
		Owned = owned;
	}
	public int getOwned() {
		return Owned;
	}
	public void setLoaned(String loaned) {
		Loaned = loaned;
	}
	public String getLoaned() {
		return Loaned;
	}
	public boolean isLoaned() {
		if (Loaned == "")
			return false;
		else
			return true;
	}
	public void save() {
		ContentValues mVals = new ContentValues();
		mVals.put(Media.TITLE, this.Title);
		mVals.put(Media.OWNED, this.Owned);
		mVals.put(Media.BARCODE, this.UPC);
		mVals.put(Media.LOANED, this.Loaned);
		cr.update(Media.CONTENT_URI, mVals, Media.BARCODE + " = " + this.UPC, null);
	}
}
```