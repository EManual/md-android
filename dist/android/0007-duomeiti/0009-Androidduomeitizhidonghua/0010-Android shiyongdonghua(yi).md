构建内容丰富的用户界面
最近几年，手机用户界面有了很大的提高，其中的一部分原因还是在于iPhone在手机UI上的创新。
在这个部分中，你将学习如何使用更加高级的UI可视效果，如阴影、半透明、动画、触摸屏和OpenGL，来进一步完善你的活动和Views。
#### 使用动画（1）
Android提供了两种类型的动画：
#### 逐帧动画 
传统的基于手机的动画，每一帧显示一个不同的图片。逐帧动画可以在一个View中显示，并使用它的Canvas(画布)作为投影屏幕。
#### 补间动画 
补间动画可以应用于View，让你可以定义一系列关于位置、大小、旋转和透明度的改变，从而让View的内容动起来。
提示：
这两种动画类型都被限制在了它们应用的原始View的界限中。由于旋转、变换以及缩放而造成的超出原始边界的部分都会被剪切掉。
#### 1. 补间动画简介
补间动画提供了一种简单的方式，以最小的资源消耗，来向用户提供深度、移动或者反馈。
使用动画来进行方向、大小、位置和透明度的改变，比通过手动地重新绘制画布来达到相似的效果要消耗更少的资源，更不用提在实现方面的简单性了。
补间动画经常用于：活动间的转换；活动内的布局间的转换；相同的View中不同的内容显示间的转换。
为用户提供下列反馈：
使用一个旋转的等待图标来指示进度或者通过"晃动"输入框来说明错误或者无效的用户输入
#### 2. 创建补间动画
补间动画是使用Animation类创建的。
下面的列表说明了可用的动画类型：
```  
AlphaAnimation  可以改变View的透明度(不透明或者部分混合)
RotateAnimation  可以在XY平面上旋转选中的View画布。
ScaleAnimation  允许你缩放选中的View
TranslateAnimation  可以在屏幕上移动选中的View(但是它只能在它的原始边界的范围内显示)
```
Android提供了AnimationSet类来对动画进行分组和配置，从而让它们作为一个集合运行。可以定义集合中的每一个动画的开始时间和持续时间，以此来控制动画序列的时刻安排和顺序。
提示：
设置每一个子动画的开始偏移时间和持续时间是很重要的，否则它们就会同时开始和结束。
下面的XML代码片段说明了如何在代码中或者作为外部资源来创建相同的动画序列：
```  
// 创建 
AnimationSet AnimationSet myAnimation = new AnimationSet(true); 
// 创建一个旋转动画 
RotateAnimation rotate = new RotateAnimation(0, 360, RotateAnimation.RELATIVE_TO_SELF,0.5f, RotateAnimation.RELATIVE_TO_SELF,0.5f ); 
rotate.setFillAfter(true); 
rotate.setDuration(1000); 
// 创建一个缩放动画 
ScaleAnimation scale = new ScaleAnimation(1, 0, 1, 0, ScaleAnimation.RELATIVE_TO_SELF,0.5f, ScaleAnimation.RELATIVE_TO_SELF, 0.5f ); 
scale.setFillAfter(true); 
scale.setDuration(500); 
scale.setStartOffset(500); 
//创建一个alpha 动画 
AlphaAnimation alpha = new AlphaAnimation(1, 0); 
scale.setFillAfter(true); 
scale.setDuration(500); 
scale.setStartOffset(500); 
//把每一个动画添加到集合中 
myAnimation.addAnimation(rotate); 
myAnimation.addAnimation(scale); 
myAnimation.addAnimation(alpha);
```
上面的代码所实现的动画序列与下面的XML片段所实现的动画序列是相同的：
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="true" >
    <rotate
        android:duration="1000"
        android:fromDegrees="0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:startOffset="0"
        android:toDegrees="360 " />
    <scale
        android:duration="500v
        android:fromXScale="1.0"
        android:fromYScale="1.0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:startOffset="500"
        android:toXScale="0.0"
        android:toYScale="0.0" />
    <alpha
        android:duration="500"
        android:fromAlpha="1.0"
        android:startOffset="500"
        android:toAlpha="0.0" />
</set>
```