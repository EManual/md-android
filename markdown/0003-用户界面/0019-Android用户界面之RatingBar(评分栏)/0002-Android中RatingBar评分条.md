RatingBar是SeekBar和ProgressBar的扩展，用星星来评级。使用的默认大小RatingBar时，用户可以触摸/拖动或使用键来设置评分，它有俩种样式（大、小），其中大的只适合指示，不适合于用户交互。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingLeft="10dip" >
    <RatingBar
        android:id="@+id/ratingbar1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:numStars="3"
        android:rating="2.5" />
    <RatingBar
        android:id="@+id/ratingbar2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:numStars="5"
        android:rating="2.25" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip" >
        <TextView
            android:id="@+id/rating"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
        <RatingBar
            android:id="@+id/small_ratingbar"
            style="?android:attr/ratingBarStyleSmall"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:layout_marginLeft="5dip" />
    </LinearLayout>
    <RatingBar
        android:id="@+id/indicator_ratingbar"
        style="?android:attr/ratingBarStyleIndicator"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="5dip" />
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.RatingBar;
import android.widget.TextView;
import android.widget.RatingBar.OnRatingBarChangeListener;
public class RatingBarDemo extends Activity implements
		OnRatingBarChangeListener {
	private RatingBar mSmallRatingBar;
	private RatingBar mIndicatorRatingBar;
	private TextView mRatingText;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.ratingbarpage);
		mRatingText = (TextView) findViewById(R.id.rating);
		// We copy the most recently changed rating on to these indicator-only
		// rating bars
		mIndicatorRatingBar = (RatingBar) findViewById(R.id.indicator_ratingbar);
		mSmallRatingBar = (RatingBar) findViewById(R.id.small_ratingbar);
		// The different rating bars in the layout. Assign the listener to us.
		((RatingBar) findViewById(R.id.ratingbar1)).setOnRatingBarChangeListener(this);
		((RatingBar) findViewById(R.id.ratingbar2)).setOnRatingBarChangeListener(this);
	}
	@Override
	public void onRatingChanged(RatingBar ratingBar, float rating,
			boolean fromUser) {
		final int numStars = ratingBar.getNumStars();
		mRatingText.setText(" 受欢迎度" + rating + "/" + numStars);
		// Since this rating bar is updated to reflect any of the other rating
		// bars, we should update it to the current values.
		if (mIndicatorRatingBar.getNumStars() != numStars) {
			mIndicatorRatingBar.setNumStars(numStars);
			mSmallRatingBar.setNumStars(numStars);
		}
		if (mIndicatorRatingBar.getRating() != rating) {
			mIndicatorRatingBar.setRating(rating);
			mSmallRatingBar.setRating(rating);
		}
		final float ratingBarStepSize = ratingBar.getStepSize();
		if (mIndicatorRatingBar.getStepSize() != ratingBarStepSize) {
			mIndicatorRatingBar.setStepSize(ratingBarStepSize);
			mSmallRatingBar.setStepSize(ratingBarStepSize);
		}
	}
}
```
效果图：
![img](P)  