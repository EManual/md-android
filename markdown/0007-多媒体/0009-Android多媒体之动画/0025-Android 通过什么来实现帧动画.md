今天我们主要讲的就是帧动画，这个在我们开发的时候会很有用的，那么我们怎么样来控制帧动画那，这个就的用android提供给我们的AnimationDrawable来控制吧，现在思路我们有了，下面就是我们来怎么样的实现了。下面我们就来看看代码是怎么样写的吧。
```  
import android.app.Activity;
import android.graphics.drawable.AnimationDrawable;
import android.os.Bundle;
import android.view.View;
import android.widget.ImageView;
[Tags]/**
 [Tags]* android中的逐帧动画. 逐帧动画的原理很简单，跟电影的播放一样，一张张类似的图片不停的切换，当切换速度达到一定值时，
 [Tags]* 我们的视觉就会出现残影，残影的出现保证了视觉上变化的连续性，这时候图片的切换看在我们眼中就跟真实的一样了。 想使用逐帧动画：
 [Tags]* 第一步：需要在res/drawable文件夹下新建一个xml文件，该文件详细定义了动画播放时所用的图片、切换每张图片
 [Tags]* 所用的时间、是否为连续播放等等。(有些文章说，在res/anim文件夹下放置该文件，事实证明，会出错哦)
 [Tags]* 第二步：在代码中，将该动画布局文件，赋值给特定的图片展示控件，如本例子中的ImageView。 第三步：通过imageView.getBackGround
 [Tags]* ()获取相应的AnimationDrawable对象，然后通过该对象的方法进行控制动画
 [Tags]*/
public class Animation1Activity extends Activity {
	ImageView imageView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.animation1);
		imageView = (ImageView) findViewById(R.id.imageView_animation1);
		imageView.setBackgroundResource(R.drawable.animation1_drawable);
	}
	public void myClickHandler(View targetButton) {
		// 获取AnimationDrawable对象
		AnimationDrawable animationDrawable = (AnimationDrawable) imageView.getBackground();
		// 动画是否正在运行
		if (animationDrawable.isRunning()) {
			// 停止动画播放
			animationDrawable.stop();
		} else {
			// 开始或者继续动画播放
			animationDrawable.start();
		}
	}
}
```
animation1.xml文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/button_animation1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:onClick="myClickHandler"
        android:text="动画开始" >
    </Button>
    <ImageView
        android:id="@+id/imageView_animation1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1" >
    </ImageView>
</LinearLayout>
```
存放动画文件的xml文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<!--
	根标签为animation-list，其中oneshot代表着是否只展示一遍，设置为false会不停的循环播放动画
	根标签下，通过item标签对动画中的每一个图片进行声明
	android:duration 表示展示所用的该图片的时间长度
-->
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false" >
    <item
        android:drawable="@drawable/a1"
        android:duration="50">
    </item>
    <item
        android:drawable="@drawable/a2"
        android:duration="50">
    </item>
    <item
        android:drawable="@drawable/a3"
        android:duration="50">
    </item>
    <item
        android:drawable="@drawable/a4"
        android:duration="50">
    </item>
    <item
        android:drawable="@drawable/a5"
        android:duration="50">
    </item>
    <item
        android:drawable="@drawable/a6"
        android:duration="50">
    </item>
</animation-list>
```
除此之外：在AnimationDrawable中，我们还可以看到如下的几个重要方法：
```  
setOneShot(boolean flag) 和在配置文件中进行配置一样，可以设置动画是否播放一次，false为连续播放；
addFrame (Drawable frame, int duration) 动态的添加一个图片进入该动画中
```