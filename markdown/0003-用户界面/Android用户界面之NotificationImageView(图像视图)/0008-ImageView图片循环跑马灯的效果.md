直接上代码了
main.xml布局文件，记住必须用RelativeLayout将ImageView重叠
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/rl"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="fill_parent"
        android:layout_height="120dp"
        android:layout_below="@+id/rl"
        android:background="@drawable/icon" />
    <ImageView
        android:id="@+id/imageView2"
        android:layout_width="fill_parent"
        android:layout_height="120dp"
        android:layout_below="@+id/rl"
        android:background="@drawable/expriment" />
    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/imageView" />
</RelativeLayout>
```
主类
```  
public class IamgeTranslatActivity extends Activity {
	[Tags]/** Called when the activity is first created. */
	public ImageView imageView;
	public ImageView imageView2;
	public Animation animation1;
	public Animation animation2;
	public TextView text;
	public boolean juage = true;
	public int images[] = new int[] { R.drawable.icon, R.drawable.expriment,
			R.drawable.changer, R.drawable.dataline, R.drawable.preffitication };
	public int count = 0;
	public Handler handler = new Handler();
	public Runnable runnable = new Runnable() {
		@Override
		public void run() {
			AnimationSet animationSet1 = new AnimationSet(true);
			AnimationSet animationSet2 = new AnimationSet(true);
			imageView2.setVisibility(0);
			TranslateAnimation ta = new TranslateAnimation(
					Animation.RELATIVE_TO_SELF, 0f, Animation.RELATIVE_TO_SELF,
					-1f, Animation.RELATIVE_TO_SELF, 0f,
					Animation.RELATIVE_TO_SELF, 0f);
			ta.setDuration(2000);
			animationSet1.addAnimation(ta);
			animationSet1.setFillAfter(true);
			ta = new TranslateAnimation(Animation.RELATIVE_TO_SELF, 1.0f,
					Animation.RELATIVE_TO_SELF, 0f, Animation.RELATIVE_TO_SELF,
					0f, Animation.RELATIVE_TO_SELF, 0f);
			ta.setDuration(2000);
			animationSet2.addAnimation(ta);
			animationSet2.setFillAfter(true);
			// iamgeView 出去 imageView2 进来
			imageView.startAnimation(animationSet1);
			imageView2.startAnimation(animationSet2);
			imageView.setBackgroundResource(images[count % 5]);
			count++;
			imageView2.setBackgroundResource(images[count % 5]);

			text.setText(String.valueOf(count));
			if (juage)
				handler.postDelayed(runnable, 6000);
			Log.i("handler", "handler");
		}
	};
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		imageView = (ImageView) findViewById(R.id.imageView);
		imageView2 = (ImageView) findViewById(R.id.imageView2);
		text = (TextView) findViewById(R.id.text);
		text.setText(String.valueOf(count));
		// 将iamgeView先隐藏，然后显示
		imageView2.setVisibility(4);
		handler.postDelayed(runnable, 2000);
	}
	public void onPause() {
		juage = false;
		super.onPause();
	}
}
```
![img](P)  