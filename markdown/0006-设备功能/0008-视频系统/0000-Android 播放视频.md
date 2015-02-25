由于Android平台由Google自己封装、设计、提供的Java Dalvik在算法处理效率上无法与C/C++或ARM、ASM相提并论，在描述或移植一些本地语言的解码器上显得无能为力，目前整个平台仅支持MP4的H.264、3GP和WMV视频解析。
Android内置的VideoView类可以快速制作一个系统播放器，VideoView主要用来显示一个视频文件，我们先开看看VideoView类的一些基本方法。 
方法说明：
```  
getBufferPercentage 得到缓冲的百分比 
getCurrentPosition 得到当前播放的位置 
getDuration 得到视频文件的时间 
isPlaying 是否正在播放 
pause 暂停 
resolveAdjustedSize 调整视频显示大小 
seekTo 指定播放位置 
setMediaController 设置播放控制器模式(播放进度条) 
setOnCompletionListener 当媒体文件播放完时触发事件 
setOnErrorListener 错误监听 
setVideoPath 设置视频源路径 
setVideoURI 设置视频源地址 
start 开始播放
```
下面是一个小例子 首先在布局文件中创建VideoView布局，并且创建几个按钮(Button) 来实现对视频的操作，当我们点击“装载” 按钮时，将指定视频文件的路径，如下代码所示：
```  
/*设置路径*/
videoView.setVideoPath("/sdcard/test.mp4");
/*设置模式-播放进度条*/
videoView.setMediaController(new MediaController(Activity01.this));
videoView.requestFocus();
```
装载之后便可以通过start、pause 方法来播放和暂停,具体代码如下
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.MediaController;
import android.widget.VideoView;
public class Activity01 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		/* 创建VideoView对象 */
		final VideoView videoView = (VideoView) findViewById(R.id.VideoView01);
		/* 操作播放的三个按钮 */
		Button PauseButton = (Button) this.findViewById(R.id.PauseButton);
		Button LoadButton = (Button) this.findViewById(R.id.LoadButton);
		Button PlayButton = (Button) this.findViewById(R.id.PlayButton);
		/* 装载按钮事件 */
		LoadButton.setOnClickListener(new OnClickListener() {
			public void onClick(View arg0) {
				/* 设置路径 */
				videoView.setVideoPath("/sdcard/test.mp4");
				/* 设置模式-播放进度条 */
				videoView.setMediaController(new MediaController(
						Activity01.this));
				videoView.requestFocus();
			}
		});
		/* 播放按钮事件 */
		PlayButton.setOnClickListener(new OnClickListener() {
			public void onClick(View arg0) {
				/* 开始播放 */
				videoView.start();
			}
		});
		/* 暂停按钮 */
		PauseButton.setOnClickListener(new OnClickListener() {
			public void onClick(View arg0) {
				/* 暂停 */
				videoView.pause();
			}
		});
	}
}
```
main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <VideoView
        android:id="@+id/VideoView01"
        android:layout_width="320px"
        android:layout_height="240px" />
    <Button
        android:id="@+id/LoadButton"
        android:layout_width="80px"
        android:layout_height="wrap_content"
        android:layout_x="30px"
        android:layout_y="300px"
        android:text="装载" />
    <Button
        android:id="@+id/PlayButton"
        android:layout_width="80px"
        android:layout_height="wrap_content"
        android:layout_x="120px"
        android:layout_y="300px"
        android:text="播放" />
    <Button
        android:id="@+id/PauseButton"
        android:layout_width="80px"
        android:layout_height="wrap_content"
        android:layout_x="210px"
        android:layout_y="300px"
        android:text="暂停" />
</AbsoluteLayout>
```