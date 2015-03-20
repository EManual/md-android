提示：用ClipDrawable。
其实用ClipDrawable不仅能实现自定义图片的进度条，也能实现很多同样的效果。
![img](http://emanual.github.io/md-android/img/view_progressbar/07_progressbar.jpg) 
```  
public class SelfDefProgressActivity extends Activity {
	private ImageView mView;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mView = new ImageView(this) {
			{
				setImageResource(R.drawable.clip_bitmap_4);
				setBackgroundResource(R.drawable.clip_btimap_content2);
				final ClipDrawable cd = (ClipDrawable) getDrawable();
				cd.setLevel(500);
				setOnClickListener(new OnClickListener() {
					public void onClick(View v) {
						if (cd.getLevel() >= 10000) {
							cd.setLevel(0);
						}
						cd.setLevel(cd.getLevel() + 500);
					}
				});
				mHandler.sendEmptyMessageDelayed(1, 500);
			}
		};
		((LinearLayout) findViewById(R.id.self_layout)).addView(mView);
	}
	private final Handler mHandler = new Handler() {
		public void handleMessage(android.os.Message msg) {
			if (mView != null) {
				mView.performClick();
				sendEmptyMessageDelayed(1, 500);
			}
		};
	};
}
```