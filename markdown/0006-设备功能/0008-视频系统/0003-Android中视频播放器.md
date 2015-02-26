视频在我们生活和娱乐当中是不可缺少的，Android为视频播放提供了VideoView和MediaController两个现成的组件，让我们可以方便的实现MP4、3GP等视频的播放。有个视频，我们发现android的功能就更加强大了。下面我们就来看看代码吧：
注意，如果播放时完全无法播放或者只有声音没有图像，你就需要换压缩软件和调整压缩参数重新压缩视频了，暂时只能这样。这一点是非常重要的，如果没有压缩好的话，我们就不会看见视频的效果。
```  
<?xml version="1.0" encoding="utf-8"?>
<LINEARLAYOUT xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="top"
    android:orientation="vertical" >
    <VIDEOVIEW
        android:id="@+id/VideoView01"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
    </VIDEOVIEW>
</LINEARLAYOUT>
```
下面的是main代码。
```  
import android.app.Activity;
import android.net.Uri;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;
import android.widget.MediaController;
import android.widget.VideoView;
public class MainVideo extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 全屏
		this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		// 标题去掉
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		// 要在全屏等设置完毕后再加载布局
		setContentView(R.layout.main);
		// 定义UI组件
		VideoView videoView = (VideoView) findViewById(R.id.VideoView01);
		// 定义MediaController对象
		MediaController mediaController = new MediaController(this);
		// 把MediaController对象绑定到VideoView上
		mediaController.setAnchorView(videoView);
		// 设置VideoView的控制器是mediaController
		videoView.setMediaController(mediaController);
		// 这两种方法都可以
		videoView.setVideoPath("[url=file:///sdcard/love_480320.mp4]file:///sdcard/love_480320.mp4[/url]");
		videoView.setVideoURI(Uri.parse("/sdcard/love_480320.mp4"));
		// 启动后就播放
		videoView.start();
	}
}
```
效果图：
![img](P)  