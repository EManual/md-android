第二种：ScaleAnimation(float fromX, float toX, float fromY, float toY, int pivotXType, floatXValue,int pivotYType, float pivotYValue)
功能：创建一个渐变尺寸伸缩动画
参数：fromX，toX分别是起始和结束时x坐标上的伸缩尺寸。fromY,toY分别是起始和结束时ye坐标上的伸缩尺寸。
pivotXValue，pivotYValue分别为伸缩动画相对于x,y坐标开始的位置，pivotXType，pivotYType分别为x,y的伸缩模式。
它有两种实现方式。
1、直接在程序中实现的方式
```  
Animation scale = new ScaleAnimation(0f, 1f, 0f, 1f, Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f); 
//设置动画持续时间 
scale.setDuration(5000); 
//开始动画 
img.startAnimation(scale);
```
2、在XML中是创建动画scale_anim.xml，在res/anim目录下
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <scale
        android:duration="5000"
        android:fillAfter="false
        android:fromXScale="0.0"
        android:fromYScale="0.0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toXScale="1.0"
        android:toYScale="1.0" />
</set>
```
Animation scale = AnimationUtils.loadAnimation(TweenActivity.this, R.anim.scale_anim);
```  
//开始动画 
img.startAnimation(scale); 
```
![img](P)  
第3种:TranslateAnimation(float fromXDelta, float toXDelta, float YDelta, float toYDelta)
功能：创建一个移动画面位置的动画
参数：fromXDelta,fromYDelta分别是其实坐标；toXDelta,toYDelta分别是结束坐标
它有两种是实现方式
1、直接在程序中实现
```  
Animation translate = new TranslateAnimation(10, 100, 10, 100); 
//设置动画持续时间 
translate.setDuration(3000); 
//开始动画 
img.startAnimation(translate); 
```
2、在XML中创建动画
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="5000"
        android:fromXDelta="10"
        android:fromYDelta="10"
        android:toXDelta="100"
        android:toYDelta="100" />
</set>
```