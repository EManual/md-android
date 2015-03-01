ProgressBar是Android的进度条。体验效果
![img](P)  
下面详细介绍ProgressBar
#### 一、说明
在某些操作的进度中的可视指示器，为用户呈现操作的进度，还它有一个次要的进度条，用来显示中间进度，如在流媒体播放的缓冲区的进度。一个进度条也可不确定其进度。在不确定模式下，进度条显示循环动画。这种模式常用于应用程序使用任务的长度是未知的。
#### 二、XML重要属性
```  
android:progressBarStyle：默认进度条样式
android:progressBarStyleHorizontal：水平样式
```
#### 三、重要方法
```  
getMax()：返回这个进度条的范围的上限
getProgress()：返回进度
getSecondaryProgress()：返回次要进度
incrementProgressBy(int diff)：指定增加的进度
isIndeterminate()：指示进度条是否在不确定模式下
setIndeterminate(boolean indeterminate)：设置不确定模式下
setVisibility(int v)：设置该进度条是否可视
```
#### 四、重要事件
```  
onSizeChanged(int w, int h, int oldw, int oldh)：当进度值改变时引发此事件
```
#### 五、实例
1.布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <ProgressBar
        android:id="@+id/progress_horizontal"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="200dip"
        android:layout_height="wrap_content"
        android:max="100"
        android:progress="50"
        android:secondaryProgress="75" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="默认进度条" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <Button
            android:id="@+id/decrease
            android:layout_width="wrap_content
            android:layout_height="wrap_content
            android:text="减少" />
        <Button
            android:id="@+id/increase"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="增加" />
    </LinearLayout>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="自定义进度条" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <Button
            android:id="@+id/decrease_secondary"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="第二减少" />
        <Button
            android:id="@+id/increase_secondary"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="第二增加" />
    </LinearLayout>
</LinearLayout>
```
2.Java代码
```  
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.widget.Button;
import android.widget.ProgressBar;
public class ProgressBarDemo extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_PROGRESS);
		setContentView(R.layout.probarpage);
		setProgressBarVisibility(true);
		final ProgressBar progressHorizontal = (ProgressBar) findViewById(R.id.progress_horizontal);
		setProgress(progressHorizontal.getProgress() * 100);
		setSecondaryProgress(progressHorizontal.getSecondaryProgress() * 100);
		Button button = (Button) findViewById(R.id.increase);
		button.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				progressHorizontal.incrementProgressBy(1);
				// Title progress is in range 0..10000
				setProgress(100 * progressHorizontal.getProgress());
			}
		});
		button = (Button) findViewById(R.id.decrease);
		button.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				progressHorizontal.incrementProgressBy(-1);
				// Title progress is in range 0..10000
				setProgress(100 * progressHorizontal.getProgress());
			}
		});
		button = (Button) findViewById(R.id.increase_secondary);
		button.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				progressHorizontal.incrementSecondaryProgressBy(1);
				// Title progress is in range 0..10000
				setSecondaryProgress(100 * progressHorizontal
						.getSecondaryProgress());
			}
		});
		button = (Button) findViewById(R.id.decrease_secondary);
		button.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				progressHorizontal.incrementSecondaryProgressBy(-1);
				// Title progress is in range 0..10000
				setSecondaryProgress(100 * progressHorizontal
						.getSecondaryProgress());
			}
		});
	}
}
```