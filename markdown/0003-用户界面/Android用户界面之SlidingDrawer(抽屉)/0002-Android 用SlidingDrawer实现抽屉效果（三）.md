代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.GridView;
import android.widget.ImageView;
import android.widget.SlidingDrawer;
import android.widget.SlidingDrawer.OnDrawerCloseListener;
import android.widget.SlidingDrawer.OnDrawerOpenListener;
import android.widget.SlidingDrawer.OnDrawerScrollListener;
public class SlidingDrawerActivity extends Activity {
	private SlidingDrawer sd;
	private GridView gv;
	private ImageView iv;
	private int[] itemIcons = new int[] { R.drawable.alarm,
			R.drawable.calendar, R.drawable.camera, R.drawable.clock,
			R.drawable.music, R.drawable.tv };
	private String[] itemString = new String[] { "Alarm", "Calendar", "camera",
			"clock", "music", "tv" };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.slidrawer);
		init();
		GridAdapter adapter = new GridAdapter(SlidingDrawerActivity.this,
				itemString, itemIcons);
		gv.setAdapter(adapter);
		/* 设定SlidingDrawer被打开的事件处理 */
		sd.setOnDrawerOpenListener(new OnDrawerOpenListener() {
			@Override
			public void onDrawerOpened() {
				iv.setImageResource(R.drawable.close);
			}
		});
		/* 设定SlidingDrawer被关闭的事件处理 */
		sd.setOnDrawerCloseListener(new OnDrawerCloseListener() {
			public void onDrawerClosed() {
				iv.setImageResource(R.drawable.open);
			}
		});
		sd.setOnDrawerScrollListener(new OnDrawerScrollListener() {
			@Override
			public void onScrollStarted() {
				System.out.println("start");
			}
			@Override
			public void onScrollEnded() {
				System.out.println("end");
			}
		});
	}
	private void init() {
		sd = (SlidingDrawer) findViewById(R.id.drawer1);
		gv = (GridView) findViewById(R.id.mycontent1);
		iv = (ImageView) findViewById(R.id.myImage);
	}
}
```