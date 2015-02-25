scale.xml 缩放效果：
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <scale
        android:duration="10000"
        android:fromXScale="0.0"
        android:fromYScale="0.0"
        android:interpolator="@android:anim/decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:repeatCount="1"
        android:repeatMode="reverse"
        android:startOffset="0"
        android:toXScale="1.5"
        android:toYScale="1.5" />
</set>
<!--
	interpolator指定动画插入器，常见的有加速减速插入器accelerate_decelerate_interpolator，加速插入器accelerate_interpolator，减速插入器decelerate_interpolator。
	fromXScale,fromYScale，动画开始前X,Y的缩放，0.0为不显示，1.0为正常大小
	toXScale，toYScale，动画最终缩放的倍数，1.0为正常大小，大于1.0放大
	pivotX，pivotY动画起始位置，相对于屏幕的百分比,两个都为50%表示动画从屏幕中间开始
	startOffset，动画多次执行的间隔时间，如果只执行一次，执行前会暂停这段时间，单位毫秒
	duration，一次动画效果消耗的时间，单位毫秒，值越小动画速度越快
	repeatCount，动画重复的计数，动画将会执行该值+1次
	repeatMode，动画重复的模式，reverse为反向，当第偶次执行时，动画方向会相反。restart为重新执行，方向不变
-->
```
activity2.java:
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class activity2 extends Activity {
	Button bt2;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity2);
		bt2 = (Button) findViewById(R.id.bt2);
		bt2.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				Intent intent = new Intent(activity2.this, main.class);
				startActivity(intent);
				overridePendingTransition(R.anim.a2, R.anim.a1);
			}
		});
	}
}
```