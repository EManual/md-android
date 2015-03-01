我们上一篇讲述了怎么才能实现你自己定义的动画效果，那么我们这一片继续上一篇的内容来学习，怎么才能完成非常吸引用户的动画效果，那就是我们这篇内容的事了。我们要做出一个吸引用花的动画从而给我们自己的应用增添砝码。到最后我们的应用就会家喻户晓。那我们还等什么，快来看看代码吧：
Animation3.java中源码如下：
```  
import com.example.android.apis.R;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.animation.AnimationUtils;
import android.view.animation.Animation;
import android.view.animation.TranslateAnimation;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Spinner;
public class Animation3 extends Activity implements
		AdapterView.OnItemSelectedListener {
	private static final String[] INTERPOLATORS = { "Accelerate", "Decelerate",
			"Accelerate/Decelerate", "Anticipate", "Overshoot",
			"Anticipate/Overshoot", "Bounce" };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.animation_3);
		Spinner s = (Spinner) findViewById(R.id.spinner);
		ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_spinner_item, INTERPOLATORS);
		adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
		s.setAdapter(adapter);
		s.setOnItemSelectedListener(this);
	}
	public void onItemSelected(AdapterView parent, View v, int position, long id) {
		final View target = findViewById(R.id.target);
		final View targetParent = (View) target.getParent();// 得到上一级
		Animation a = new TranslateAnimation(0.0f, targetParent.getWidth()
				- target.getWidth() - targetParent.getPaddingLeft()
				- targetParent.getPaddingRight(), 0.0f, 0.0f);
		a.setDuration(1000);
		a.setStartOffset(300);
		a.setRepeatMode(Animation.RESTART);// restart
		a.setRepeatCount(Animation.INFINITE);
		switch (position) {
		case 0:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.accelerate_interpolator));
			break;
		case 1:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.decelerate_interpolator));
			break;
		case 2:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.accelerate_decelerate_interpolator));
			break;
		case 3:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.anticipate_interpolator));
			break;
		case 4:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.overshoot_interpolator));
			break;
		case 5:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.anticipate_overshoot_interpolator));
			break;
		case 6:
			a.setInterpolator(AnimationUtils.loadInterpolator(this,
					android.R.anim.bounce_interpolator));
			break;
		}
		target.startAnimation(a);
	}
	public void onNothingSelected(AdapterView parent) {
	}
}
```
animation_3.xml文件源码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:clipToPadding="false"
    android:orientation="vertical"
    android:padding="10dip" >
    <TextView
        android:id="@+id/target"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/animation_3_text"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="5dip"
        android:layout_marginTop="20dip"
        android:text="@string/animation_2_instructions" />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```