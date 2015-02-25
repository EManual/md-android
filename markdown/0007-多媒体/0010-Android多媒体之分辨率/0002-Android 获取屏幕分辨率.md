代码如下：
```  
import android.app.Activity;
import android.content.res.Resources;
import android.graphics.Color;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.util.DisplayMetrics;
import android.widget.TextView;
public class AcquireResolution extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		TextView tv = (TextView) findViewById(R.id.textView);
		Resources resources = getBaseContext().getResources();
		Drawable drawable = resources.getDrawable(R.drawable.red);
		tv.setBackgroundDrawable(drawable);
		tv.setTextColor(Color.GREEN);
		DisplayMetrics dm = new DisplayMetrics();
		getWindowManager().getDefaultDisplay().getMetrics(dm);
		tv.setText("屏幕分辨率为:" + dm.widthPixels + " * " + dm.heightPixels);
	}
}
```