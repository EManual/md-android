在Android 中， Animation 动画效果的实现可以通过两种方式进行实现，一种是 tweened animation 渐变动画，另一种是 frame by frame animation 画面转换动画。
tweened animation 渐变动画有以下两种类型：
1.alpha 渐变透明度动画效果。
2.scale 渐变尺寸伸缩动画效果。
frame by frame animation 画面转换动画有以下两种类型：
1.translate 画面转换位置移动动画效果。
2.rotate 画面转移旋转动画效果。
在res文件夹下新建一个anim的文件夹，并在其中建立一个 animation.xml 文件，具体如下：
```  
<?xml version="1.0" encoding="utf-8"?> 
<set xmlns:android="http://schemas.android.com/apk/res/android"> 
<translate 
	android:fromXDelta="0" // 设置动画开始时 x 坐标的位置
	android:toXDelta="-100%p" // 设置动画结束时 x 坐标的位置
	android:duration="300" // 设置动画持续的时间 300 毫秒
	>
</translate> 
<alpha 
	android:fromAlpha="1.0" // 设置动画开始时的透明度 1.0 代表不透明
	android:toAlpha="0.0" // 设置动画开始时的透明度 0.0 表示完全透明
	android:duration="300" // 设置动画持续的时间 300 毫秒
	/>
<scale 
	android:interpolator=" // 设置动画出入器
	android:anim/accelerate_decelerate_interpolator"
	android:fromXScale="0.0" // 设置动画开始时 x 坐标上的伸缩长度
	android:toXScale="1.4" // 设置动画结束时 x 坐标上的伸缩长度
	android:fromYScale="0.0" // 设置动画开始时 y 坐标上的伸缩长度
	android:toYScale="1.4" // 设置动画开始时 y 坐标上的伸缩长度
	android:pivotX="50%" // 设置动画相对于控件的 x 坐标的位置
	android:pivotY="50%" // 设置动画相对于控件的 y 坐标的位置
	android:fillAfter="false" // 该动画转化在动画结束前开始应用
	android:duration="700" // 设置动画持续的时间
	/>
<rotate 
	android:interpolator= // 设置动画出入器
	"@android:anim/accelerate_decelerate_interpolator" 
	android:fromDegrees="0" // 设置动画开始时的角度
	android:toDegrees="+350" // 设置动画结束时的旋转角度
	android:pivotX="50%" // 设置动画相对于控件的 x 坐标的位置
	android:pivotY="50%" // 设置动画相对于控件的 y 坐标的位置
	android:duration="3000" // 设置动画持续的时间
	/>
</set> 
```
代码如下：
```  
Animation animation;
animation = AnimationUtils.loadAnimation(this, R.anim.animation);
// 然后再想要实现动画效果的控件上通过使用 startAnimation() 方法进行添加。
// 编写动画对象，并且获取自定应的动画样式
animation = AnimationUtils.loadAnimation(this, R.anim.animation);
spinner.setOnTouchListener(new Spinner.OnTouchListener() {
	@Override
	public boolean onTouch(View v, MotionEvent event) {
		// 运行动画 animation
		v.startAnimation(animation);
		// 将 spinner 的可见性设置为不可见状态
		v.setVisibility(View.INVISIBLE);
		return false;
	}
});
```