Android的动画效果分为两种，一种是tweened animation(补间动画），第二种是frame by frame animation。一般我们用的是第一种。补间动画又分为AlphaAnimation,透明度转换 RotateAnimation,旋转转换 ScaleAnimation,缩放转换 TranslateAnimation 位置转换（移动）。
动画效果在anim目录下的xml文件中定义，在程序中用AnimationUtils.loadAnimation(Context context,int ResourcesId)载入成Animation对象，在需要显示动画效果时，执行需要动画的View的startAnimation方法，传入Animation,即可。切换Activity也可以应用动画效果，在startActivity方法后，执行overridePendingTransition方法，两个参数分别是切换前的动画效果，切换后的动画效果，下面的例子中传入的是两个alpha动画,以实现切换Activity时淡出淡入，渐隐渐现效果。
下面贴出代码：
两个Activity的布局文件 main.xml:
activity2.xml:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/ll2"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Activity2" />
    <Button
        android:id="@+id/bt2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="返回main" />
</LinearLayout>
```
动画效果XML文件，全部存放在anim目录下：
a1.xml 淡出效果
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <alpha
        android:duration="500"
        android:fromAlpha="1.0"
        android:toAlpha="0.0" />
</set>
<!--
	fromAlpha:开始时透明度
	toAlpha：结束时透明度
	duration：动画持续时间
-->
```
a2.xml 淡入效果：
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <alpha
        android:duration="500"
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />
</set>
```
rotate.xml 旋转效果：
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <rotate
        android:duration="10000"
        android:fromDegrees="300"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="10%"
        android:pivotY="100%"
        android:toDegrees="-360" />

</set>
<!--
	fromDegrees开始时的角度
	toDegrees动画结束时角度
	pivotX,pivotY不太清楚，看效果应该是定义旋转的圆心的
-->
```