我们看到了上面的标题，可拖动的进度条控件，也就是说用户可以自己来控制进度条，这个空间主要应用在视频播放器和音乐播放器。这么说吧，当你在看电影的时候，突然有事了要出去，不能看，你会记住你看到了什么地方，如果这个视频播放器有个可拖动的进度条控件，我们就可以一下就拖动到你想看的地方，这样会给我们的用户节省很多的时间。现在视频播放器都有可拖动的进度条控件。呵呵，下面我们就来看看代码吧：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!--
		SeekBar - 可拖动的进度条控件
		max - 进度的最大值
		progress - 第一进度位置
		secondaryProgress - 第二进度位置
    -->
    <SeekBar
        android:id="@+id/seekBar"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:max="100"
        android:progress="50"
        android:secondaryProgress="75" />
    <TextView
        android:id="@+id/progress"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <TextView
        android:id="@+id/tracking"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.SeekBar;
import android.widget.TextView;
public class _SeekBar extends Activity implements
		SeekBar.OnSeekBarChangeListener {
	SeekBar mSeekBar;
	TextView mProgressText;
	TextView mTrackingText;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.setContentView(R.layout.seekbar);
		setTitle("SeekBar");
		mSeekBar = (SeekBar) findViewById(R.id.seekBar);
		// setOnSeekBarChangeListener() - 响应拖动进度条事件
		mSeekBar.setOnSeekBarChangeListener(this);
		mProgressText = (TextView) findViewById(R.id.progress);
		mTrackingText = (TextView) findViewById(R.id.tracking);
	}
	// 拖动进度条后，进度发生改变时的回调事件
	public void onProgressChanged(SeekBar seekBar, int progress,
			boolean fromTouch) {
		mProgressText.setText(progress + "%");
	}
	// 拖动进度条前开始跟踪触摸
	public void onStartTrackingTouch(SeekBar seekBar) {
		mTrackingText.setText("开始跟踪触摸");
	}
	// 拖动进度条后停止跟踪触摸
	public void onStopTrackingTouch(SeekBar seekBar) {
		mTrackingText.setText("停止跟踪触摸");
	}
}
```