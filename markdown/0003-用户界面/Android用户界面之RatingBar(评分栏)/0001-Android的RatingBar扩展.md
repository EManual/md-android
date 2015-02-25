和SeekBar类似，RatingBar也是扩展，只不过它是SeekBar和ProgressBar的扩展，它是用星星来显示等级。当使用默认尺寸的RatingBar时，用户能够触摸/拖动或使用箭头键来设置评级。较小的RatingBar类型(ratingBarStyleSmall)以及较大的单一指示器类型(ratingBarStyleIndicator)都不支持用户交互，只适用于指示器。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <RatingBar
        android:id="@+id/ratingbar_Indicator"
        style="android:attr/ratingBarStyleIndicator"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
    <RatingBar
        android:id="@+id/ratingbar_Small"
        style="android:attr/ratingBarStyleSmall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:numStars="20" />
    <RatingBar
        android:id="@+id/ratingbar_default"
        style="android:attr/ratingBarStyle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.RatingBar;
import android.widget.Toast;
public class AndroidRatingBar extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		final RatingBar ratingBar_Small = (RatingBar) findViewById(R.id.ratingbar_Small);
		final RatingBar ratingBar_Indicator = (RatingBar) findViewById(R.id.ratingbar_Indicator);
		final RatingBar ratingBar_default = (RatingBar) findViewById(R.id.ratingbar_default);
		ratingBar_default.setOnRatingBarChangeListener(new RatingBar.OnRatingBarChangeListener() {
					@Override
					public void onRatingChanged(RatingBar ratingBar,
							float rating, boolean fromUser) {
						ratingBar_Small.setRating(rating);
						ratingBar_Indicator.setRating(rating);
						Toast.makeText(AndroidRatingBar.this,
								"rating:" + String.valueOf(rating),
								Toast.LENGTH_LONG).show();
					}
				});
	}
}
```