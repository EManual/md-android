Frame动画其实就是逐帧动画，用法也比Tween动画简单，只需要创建一个AnimationDrawable对象来表示Frame动画，然后通过addFrame方法把每一帧要显示的内容加进去就行了，最后通过start方法就可以播放这个动画了，通过还可以使用setOneShot()方法来设置动画是否重复播放。
再这里，还需要设置图片的所在位置，首先要在res/anim目录下创建一个xml配置文件，用于存放图片资源的索引，配置的是一个以<animation-list>根原素和<item>子元素
下面用3种方式来实现这个Frame动画
第一种：直接继承Activity，使用<animation-list>列表来实现
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="逐帧动画" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <Button
            android:id="@+id/start"
            android:layout_width="150dp"
            android:layout_height="wrap_content"
            android:text="开始播放动画" />
        <Button
            android:id="@+id/stop"
            android:layout_width="150dp"
            android:layout_height="wrap_content"
            android:text="停止播放动画" />
    </LinearLayout>
    <ImageView
        android:id="@+id/imgview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:background="@anim/birthday" />
</LinearLayout>
```
res/anim/birthday.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true" >
    <item
        android:drawable="@drawable/birthday1"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday2"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday3"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday4"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday5"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday6"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday7"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday8"
        android:duration="300"/>
    <item
        android:drawable="@drawable/birthday9"
        android:duration="300"/>
</animation-list>
```
FramesActivity.java
```  
import android.app.Activity;
import android.graphics.drawable.AnimationDrawable;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
public class FramesActivity extends Activity {
	private AnimationDrawable frameanim;
	private Button start, stop;
	private ImageView img;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		img = (ImageView) findViewById(R.id.imgview);
		start = (Button) findViewById(R.id.start);
		stop = (Button) findViewById(R.id.stop);
		// 获得背景色，并转换为AnimationDrawable对象
		frameanim = (AnimationDrawable) img.getBackground();
		// 为按钮添加监听事件
		start.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				// 开始动画
				frameanim.start();
			}
		});
		stop.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				// 停止动画
				frameanim.stop();
			}
		});
	}
}
```