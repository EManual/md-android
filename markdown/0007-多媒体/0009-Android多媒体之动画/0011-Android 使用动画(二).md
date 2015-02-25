3. 应用补间动画
通过调用startAnimation方法，可以将动画应用到任意的View中，只需传递给这个方法要应用的动画或者动画集合即可。
动画序列将会运行一次，然后停止，除非使用动画或者动画集合中的setRepeatMode和setRepeatCount方法修改了这种行为。可以通过把重复模式设置为RESTART或者REVERSE，来强制动画循环或者正向反向运行。设置重复计数可以控制动画重复的次数。
下面的代码展示了一个无限循环的动画：
```  
myAnimation.setRepeatMode(Animation.RESTART); 
myAnimation.setRepeatCount(Animation.INFINITE); 
myView.startAnimation(myAnimation);
```
4. 使用动画监听器
AnimationListener可以让你创建一个事件处理程序，当动画开始或者结束的时候触发它。这可以在动画开始之前或者结束之后执行某些动作，例如，改变View的内容，或者链接多个动画。
可以对一个Animation对象调用setAnimationListener，并传递给它一个新的AnimationListener实现，同时按要求重写onAnimationEnd、 onAnimationStart和 onAnimationRepeat。
下面的框架代码展示了一个Animation Listener的基本实现：
```  
myAnimation.setAnimationListener(new AnimationListener() {
	public void onAnimationEnd(Animation _animation) {
		// 待实现：动画完成之后要做的事情
	}
	public void onAnimationRepeat(Animation _animation) {
		// 待实现：动画重复的时候做的事情
	}
	public void onAnimationStart(Animation _animation) {
		// 待实现：动画开始的时候做的事情
	}
});
```
5. 具有滑动动画效果的用户界面示例
在这个例子中，将创建一个新的活动，它使用一个动画，并根据D-pad按下的方向，平滑地改变用户界面的内容。
(1) 创建一个新的ContentSlider项目，其中包含一个ContentSLider活动。
```  
import android.app.Activity;
import android.view.KeyEvent;
import android.os.Bundle;
import android.view.animation.Animation;
import android.view.animation.Animation.AnimationListener;
import android.view.animation.AnimationUtils;
import android.widget.TextView;
public class ContentSlider extends Activity {
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.main);
	}
}
```
(2) 修改main.xml布局资源。它应该包含一个简单的TextView，且该TextView中的文本是粗体、居中并且相对较大的。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/myTextView"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_margin="10px"
        android:editable="false
        android:gravity="center"
        android:singleLine="true
        android:text="CENTER"
        android:textSize="30sp"
        android:textStyle="bold" />
</LinearLayout>
```
(3) 创建一系列动画，将当前的View分别从上、下、左、右各个方向上滑出，并将下一个View分别从相应的方向上滑入。每一个动画都应该有它自己的文件。
a. 创建slide_bottom_in.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromYDelta="-100%p"
        android:toYDelta="0" />
</set>
```
b. 创建slide_bottom_out.xml
```  
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/accelerate_interpolator" >
    <translate
        android:duration="700"
        android:fromYDelta="0"
        android:toYDelta="100%p" />
</set>
```