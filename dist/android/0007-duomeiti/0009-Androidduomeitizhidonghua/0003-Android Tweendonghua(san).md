在程序中可以定义其实现，比如在Button中的按钮事件。
```  
Animation translate = AnimationUtils.loadAnimation(TweenActivity.this, R.anim.translate_anim); 
//开始动画 
img.startAnimation(translate); 
```

![img](http://emanual.github.io/md-android/img/media_animation/04_animation.jpg) 

第4种：Rotate(float fromDegrees, float toDegrees, int pivotXType, float pivotXValue, int pivotYType,
float pivotYValue)
功能：创建一个旋转画面的动画
参数：fromDegrees为开始的角度；toDegrees为结束的角度。pivotXValue、pivotYType分别为x,y的伸缩模式。
pivotXValue，pivotYValue分别为伸缩动画相对于x,y的坐标开始位置。
它有两种实现方式。
1、直接在程序中创建动画
```  
Animation rotate = new RotateAnimation(0f,+360f, 
Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF, 0.5f); 
rotate.setDuration(3000); 
img.startAnimation(rotate); 
```
2、在XML中创建动画 rotate_anim.xml 在res/anim目录下
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <rotate
        android:duration="1000"
        android:fromDegrees="0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toDegrees="+360" />
</set>
Animation rotate = AnimationUtils.loadAnimation(TweenActivity.this, R.anim.rotate_anim); 
img.startAnimation(rotate); 
```

![img](http://emanual.github.io/md-android/img/media_animation/04_animation.jpg) 
