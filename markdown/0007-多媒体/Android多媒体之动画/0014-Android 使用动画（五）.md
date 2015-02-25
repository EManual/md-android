#### 创建布局动画
要创建一个新的布局动画，首先要定义一个动画并将其应用到每一个子View中。然后，在代码中或者作为外部动画资源，创建一个新的LayoutAnimation，它引用了要应用的动画以及应用它的顺序和时刻安排。
下面的XML代码段展示了一个简单的动画定义，该动画作为popin.xml文件存储在res/anim文件夹下，在这个文件夹下还有一个存储为popinlayout.xml的布局动画。
#### 使用动画（5）
Layout Animation会随机地将一个简单的"pop-in"动画应用到被分配到任何View Group中的每一个子View中。
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <scale
        android:duration="400"
        android:fromXScale="0.0"
        android:fromYScale="0.0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toXScale="1.0"
        android:toYScale="1.0" />
</set> 
```
res/anim/popinlayout.xml 
```  
<layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android"
    android:animation="@anim/popin"
    android:animationOrder="random"
    android:delay="0.5" />
```
#### 使用布局动画(Layout Animation)
一旦定义了一个布局动画，那么就可以使用代码或使用布局XML资源将其应用到一个ViewGroup中。在XML中，这是通过在布局定义中使用android:layoutAnimation来完成的，如下面的XML代码所示：
```  
android:layoutAnimation="@anim/popinlayout"
```
要在代码中设置一个布局动画，可以对View Group调用setLayoutAnimation，并给它传递所希望应用的LayoutAnimation对象的引用。
在这两种情况下，布局动画都会在View Group第一次进行布局的时候执行一次。可以通过对ViewGroup对象调用scheduleLayoutAnimation，强制它再次执行。那么，当ViewGroup下次被布局的时候，这个动画就会再次执行。
Layout Animation也支持Animation Listener。
在下面的代码段中，一个ViewGroup的动画在重新运行的同时，还附加了一个Listener，从而在动画完成的时候触发额外的动作：
```  
aViewGroup.setLayoutAnimationListener(new AnimationListener() {
	public void onAnimationEnd(Animation _animation) {
		// 待实现：动画完成时的动作
	}
	public void onAnimationRepeat(Animation _animation) {
	}
	public void onAnimationStart(Animation _animation) {
	}
});
aViewGroup.scheduleLayoutAnimation();
```
#### 7. 创建和使用逐帧动画
逐帧动画与传统的基于手机的卡通动画很相似，都是根据帧来选择图片。补间动画是使用目标View来提供动画的内容，而逐帧动画则让你可以指定一系列Drawable对象用作一个View的背景。
AnimationDrawable类可以用来创建一个新的表示一个Drawable资源的逐帧动画。
可以使用XML，在应用程序的drawable目录下将Animation Drawable资源定义为外部资源。可以使用animation-list标签来分组一个item标签集合，其中每一个item标签都使用一个drawable属性来定义要显示的图片，并且使用一个duration属性来指定显示它的时间(以毫秒为单位)。
下面的XML代码展示了如何创建一个简单的动画来演示火箭的起飞。该文件被存储为res/drawable/animated_rocket.xml：
```  
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false" >
    <item
        android:drawable="@drawable/rocket1"
        android:duration="500"/>
    <item
        android:drawable="@drawable/rocket2"
        android:duration="500"/>
    <item
        android:drawable="@drawable/rocket3"
        android:duration="500"/>
</animation-list>
```
要显示动画，可以通过使用setBackgroundResource方法将其设置为一个View的背景，如下面的代码段所示：
```  
ImageView image = (ImageView)findViewById(R.id.my_animation_frame); 
image.setBackgroundResource(R.drawable.animated_rocket);
```
或者，可以使用setBackgroundDrawable方法来使用一个Drawable实例而不是一个资源引用。调用它的start方法运行动画，如下面的代码所示：
```  
AnimationDrawable animation = (AnimationDrawable)image.getBackground(); animation.start();
```