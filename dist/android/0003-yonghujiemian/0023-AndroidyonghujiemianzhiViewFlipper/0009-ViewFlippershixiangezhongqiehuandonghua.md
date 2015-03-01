主要就是main.xml代码：
```  
<ViewFlipper
    android:id="@+id/flipper"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:flipInterval="2000" >
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="aaaaaaaaa"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="bbbbbbbb"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="ccccccccc"
        android:textSize="26sp" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:text="dddddddddd"
        android:textSize="26sp" />
</ViewFlipper>

mFlipper = (ViewFlipper) findViewById(R.id.flipper);
// 以下是各种动画设置
// 向上消失
mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,
		R.anim.push_up_in));
mFlipper.setOutAnimation(AnimationUtils.loadAnimation(this,
		R.anim.push_up_out));
// 旋转消失
mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,
		R.anim.hyperspace_in));
mFlipper.setOutAnimation(AnimationUtils.loadAnimation(this,
		R.anim.hyperspace_out));
// 动画循环切换各个子控件
mFlipper.startFlipping();
```
二、动画
```  
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="300"
        android:fromYDelta="100%p"
        android:toYDelta="0" />
    <alpha
        android:duration="300"
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />
</set>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="300"
        android:fromYDelta="0"
        android:toYDelta="-100%p" />
    <alpha
        android:duration="300"
        android:fromAlpha="1.0"
        android:toAlpha="0.0" />
</set>
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="300"
    android:fromAlpha="0.0"
    android:startOffset="1200"
    android:toAlpha="1.0" />
　　<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false" >
    <scale
        android:duration="700"
        android:fillAfter="false"
        android:fromXScale="1.0"
        android:fromYScale="1.0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toXScale="1.4"
        android:toYScale="0.6" />
    <set
        android:interpolator="@android:anim/accelerate_interpolator"
        android:startOffset="700" >
        <scale
            android:duration="400"
            android:fromXScale="1.4"
            android:fromYScale="0.6"
            android:pivotX="50%"
            android:pivotY="50%"
            android:toXScale="0.0"
            android:toYScale="0.0" />
        <rotate
            android:duration="400"
            android:fromDegrees="0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:toDegrees="-45"
            android:toYScale="0.0" />
    </set>
</set>
```