c. 创建slide_top_in.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromYDelta="100%p"
        android:toYDelta="0" />
</set>
```
d. 创建slide_top_out.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromYDelta="0"
        android:toYDelta="-100%p" />
</set>

```
e. 创建slide_left_in.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromXDelta="100%p"
        android:toXDelta="0" />
</set>
```
f. 创建slide_left_out.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromXDelta="0"
        android:toXDelta="-100%p" />
</set>
```
g. 创建slide_right_in.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromXDelta="-100%p"
        android:toXDelta="0" />
</set>
```
h. 创建slide_right_out.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromXDelta="0"
        android:toXDelta="100%p" />
</set>
```
(4) 返回到ContentSlider活动，获得对TextView以及你在第(3)步中创建的每一个动画的引用。
```  
Animation slideInLeft;
Animation slideOutLeft;
Animation slideInRight;
Animation slideOutRight;
Animation slideInTop;
Animation slideOutTop;
Animation slideInBottom;
Animation slideOutBottom;
TextView myTextView;
@Override
public void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	setContentView(R.layout.main);
	slideInLeft = AnimationUtils.loadAnimation(this, R.anim.slide_left_in);
	slideOutLeft = AnimationUtils
			.loadAnimation(this, R.anim.slide_left_out);
	slideInRight = AnimationUtils
			.loadAnimation(this, R.anim.slide_right_in);
	slideOutRight = AnimationUtils.loadAnimation(this,
			R.anim.slide_right_out);
	slideInTop = AnimationUtils.loadAnimation(this, R.anim.slide_top_in);
	slideOutTop = AnimationUtils.loadAnimation(this, R.anim.slide_top_out);
	slideInBottom = AnimationUtils.loadAnimation(this,
			R.anim.slide_bottom_in);
	slideOutBottom = AnimationUtils.loadAnimation(this,
			R.anim.slide_bottom_out);
	myTextView = (TextView) findViewById(R.id.myTextView);
}
```
每一次屏幕转换都由连接到一起的两个动画组成：滑出旧的文本，滑入新的文本。你并不需要创建多个Views，而是可以在View滑出屏幕之后，并从相反的方向滑入屏幕之前，改变它的值。
(5) 创建一个新的方法，应用一个滑出动画，然后在完成文本内容的修改之后，再启动滑入动画。
```  
private void applyAnimation(Animation _out, Animation _in, String _newText) {
	final String text = _newText;
	final Animation in = _in;
	// 保证滑出动画完成之后，文本也不在屏幕上。
	_out.setFillAfter(true);
	// 创建一个监听器来等待滑出动画的完成
	_out.setAnimationListener(new AnimationListener() {
		public void onAnimationEnd(Animation _animation) {
			// 改变文本
			myTextView.setText(text);
			// 把它滑动回View上
			myTextView.startAnimation(in);
		}
		public void onAnimationRepeat(Animation _animation) {
		}
		public void onAnimationStart(Animation _animation) {
		}
	});
	// 启动滑出动画
	myTextView.startAnimation(_out);
}
```