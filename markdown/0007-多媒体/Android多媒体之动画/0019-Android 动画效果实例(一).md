大家都应该知道，当我们在应用中没有动画效果的话，那么你的应用做的真的会没有一个人玩，这个我都不用多说什么，大家就应该明白了，当我们的应用有了很炫的动画时，你这个就是一个比较不错的应用了，因为大多数的人还是比较爱看动画的，那么我们今天就教大家怎么样才能实现动画效果，废话不多说，来看看代码吧：
1.在图片显示过程中使用动画效果，可以给人一种感觉。比如渐进渐出的效果。
```  
mSwitcher = (ImageSwitcher) findViewById(R.id.switcher); 
mSwitcher.setFactory(this); 
mSwitcher.setInAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_in)); 
mSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_out)); 
```
android.R.anim.fade_in， android.R.anim.fade_out两种动画效果是系统自带的效果。
2.下面介绍自定义的动画效果。
```  
// 实现动画效果 
Animation shake = AnimationUtils.loadAnimation(this, R.anim.shake); 
findViewById(R.id.pw).startAnimation(shake); 
```
里面用到的shake.xml文件，存放在anim目录下面。代码如下：
```  
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:fromXDelta="0"
    android:interpolator="@anim/cycle_7"
    android:toXDelta="10" />
```
而里面的cycle_7.xml，代码如下所示：
```  
<cycleInterpolator xmlns:android="http://schemas.android.com/apk/res/android"
    android:cycles="7" />
```
下面介绍APIDEMO中的动画效果。
第一种ViewFlipper中各背景图片的切换效果。
```  
public class Animation2 extends Activity implements AdapterView.OnItemSelectedListener {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.animation_2);
		mFlipper = ((ViewFlipper) this.findViewById(R.id.flipper));
		mFlipper.startFlipping();
		Spinner s = (Spinner) findViewById(R.id.spinner);
		ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_spinner_item, mStrings);
		adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
		s.setAdapter(adapter);
		s.setOnItemSelectedListener(this);
	}
	public void onItemSelected(AdapterView parent, View v, int position, long id) {
		switch (position) {
		case 0:
			mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,
					R.anim.push_up_in));
			mFlipper.setOutAnimation(AnimationUtils.loadAnimation(this,
					R.anim.push_up_out));
			break;
		case 1:
			mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,
					R.anim.push_left_in));
			mFlipper.setOutAnimation(AnimationUtils.loadAnimation(this,
					R.anim.push_left_out));
			break;
		case 2:
			mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,
					android.R.anim.fade_in));
			mFlipper.setOutAnimation(AnimationUtils.loadAnimation(this,
					android.R.anim.fade_out));
			break;
		default:
			mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,
					R.anim.hyperspace_in));
			mFlipper.setOutAnimation(AnimationUtils.loadAnimation(this,
					R.anim.hyperspace_out));
			break;
		}
	}
	public void onNothingSelected(AdapterView parent) {
	}
	private String[] mStrings = { "Push up", "Push left", "Cross fade", "Hyperspace" };
	private ViewFlipper mFlipper;
}
```
animation_2.xml文件如下：
```  
<ViewFlipper
    android:id="@+id/flipper"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginBottom="20dip"
    android:flipInterval="2000" >
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="@string/animation_2_text_1"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="@string/animation_2_text_2"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="@string/animation_2_text_3"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="@string/animation_2_text_4"
        android:textSize="26sp" />
</ViewFlipper>
<TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginBottom="5dip"
    android:text="@string/animation_2_instructions" />
<Spinner
    android:id="@+id/spinner"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```