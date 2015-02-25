代码量比较少，所以不做多的解释，直接贴图贴代码。
![img](P)  
animation_in.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="2000"
        android:fromXDelta="100%p"
        android:toXDelta="0" />
    <alpha
        android:duration="2000"
        android:fromAlpha="0"
        android:toAlpha="1" />
</set>
```
animation_out.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="2000"
        android:fromXDelta="0"
        android:toXDelta="-100%p" />
    <alpha
        android:duration="2000"
        android:fromAlpha="1.0"
        android:toAlpha="0.0" />
</set>
```
main.java
```  
public class main extends Activity {
	[Tags]/** Called when the activity is first created. */
	private ViewFlipper flipper;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		flipper = new ViewFlipper(this);
		flipper.addView(getImageViewById(R.drawable.a1));
		flipper.addView(getImageViewById(R.drawable.a2));
		flipper.addView(getImageViewById(R.drawable.a3));
		flipper.addView(getImageViewById(R.drawable.a4));
		flipper.addView(getImageViewById(R.drawable.a5));
		flipper.setBackgroundColor(Color.BLACK);
		flipper.setOutAnimation(AnimationUtils.loadAnimation(this,
				R.anim.animation_out));
		flipper.setInAnimation(AnimationUtils.loadAnimation(this,
				R.anim.animation_in));
		flipper.setFlipInterval(5000);// 每隔5秒自动显示ViewFlipper里面的图片
		flipper.startFlipping();// 开始显示
		setContentView(flipper);
	}
	ImageView getImageViewById(int id) {
		ImageView imageView = new ImageView(this);
		Drawable draw;
		draw = this.getResources().getDrawable(id);
		imageView.setBackgroundDrawable(draw);
		return imageView;
	}
}
```