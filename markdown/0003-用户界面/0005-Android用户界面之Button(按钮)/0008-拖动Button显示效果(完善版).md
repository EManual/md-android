使得Buttton不能拖动出屏幕范围，代码如下：
```  
public class DraftTest extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		DisplayMetrics dm = getResources().getDisplayMetrics();
		final int screenWidth = dm.widthPixels;
		final int screenHeight = dm.heightPixels - 50;
		final Button b = (Button) findViewById(R.id.btn);
		b.setOnTouchListener(new OnTouchListener() {
			int lastX, lastY;
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				int ea = event.getAction();
				Log.i("TAG", "Touch:" + ea);
				switch (ea) {
				case MotionEvent.ACTION_DOWN:
					lastX = (int) event.getRawX();
					lastY = (int) event.getRawY();
					break;
				 /**
				  * layout(l,t,r,b) l Left position, relative to parent t Top
				  * position, relative to parent r Right position, relative to
				  * parent b Bottom position, relative to parent
				  *  */
				case MotionEvent.ACTION_MOVE:
					int dx = (int) event.getRawX() - lastX;
					int dy = (int) event.getRawY() - lastY;
					int l = v.getLeft() + dx;
					int b = v.getBottom() + dy;
					int r = v.getRight() + dx;
					int t = v.getTop() + dy;
					if (l < 0) {
						l = 0;
						r = l + v.getWidth();
					}
					if (t < 0) {
						t = 0;
						b = t + v.getHeight();
					}
					if (r > screenWidth) {
						r = screenWidth;
						l = r - v.getWidth();
					}
					if (b > screenHeight) {
						b = screenHeight;
						t = b - v.getHeight();
					}
					v.layout(l, t, r, b);
					lastX = (int) event.getRawX();
					lastY = (int) event.getRawY();
					Toast.makeText(DraftTest.this,
							"???λ???" + l + "," + t + "," + r + "," + b,
							Toast.LENGTH_SHORT).show();
					v.postInvalidate();
					break;
				case MotionEvent.ACTION_UP:
					break;
				}
				return false;
			}
		});
	}
}
```
运行效果如下：
![img](P)  
![img](P)  
![img](P)  