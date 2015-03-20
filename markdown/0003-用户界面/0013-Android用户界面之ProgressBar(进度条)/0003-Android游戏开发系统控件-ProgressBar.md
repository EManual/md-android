ProgressBar(运行进度条)是较常用到的组件，例如下载进度，安装程序进度、加载资源进度显示条等。在Android中提供了两种样式分别表示在不同状态下显示的进度条，下面来实现这两种样式。
创建项目："ProgressBarProject"
功能：显示两种样式的进度条
项目运行效果图：
![img](http://emanual.github.io/md-android/img/view_progressbar/04_progressbar.jpg) 
项目代码
修改布局文件
main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/tv1" />
    <ProgressBar
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/pb1" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/tv2" />
    <ProgressBar
        style="?android:attr/progressBarStyleSmall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/pb2" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/tv3" />
    <ProgressBar
        style="?android:attr/progressBarStyleLarge"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/pb3" />
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/tv4" />
    <ProgressBar
        android:id="@+id/pb"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:max="100"
        android:progress="50"
        android:secondaryProgress="70"
        android:text="string/pb4" />
</LinearLayout>
```
修改values文件
string.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<string name="hello">Hello World, ProgressBarActivity!</string>
	<string name="app_name">ProgressBar</string>
	<string name="tv1">默认进度条</string>
	<string name="tv2">小圆形进度条</string>
	<string name="tv3">大圆形进度条</string>
	<string name="tv4">条形进度条</string>
	<string name="pb1">progress1</string>
	<string name="pb2">progress2</string>
	<string name="pb3">progress3</string>
	<string name="pb4">progress4</string>
</resources>
```
修改MainActivity文件
ProgressBarActivity.java
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.ProgressBar;
public class ProgressBarActivity extends Activity implements Runnable {
	private Thread th; // 声明一条线程
	private ProgressBar pb; // 声明一个进度条对象
	private boolean stateChange;// 标识进度值最大最小的状态
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		pb = (ProgressBar) findViewById(R.id.pb);
		th = new Thread(this);// 实例线程对象
		th.start(); // 启动线程
	}
	public void run() { // 实现Runable接口抽象函数
		while (true) {
			int current = pb.getProgress();// 得到当前进度值
			int currentMax = pb.getMax();// 得到进度条的最大进度值
			int secCurrent = pb.getSecondaryProgress();// 得到底层当前进度值
			// 以下代码实现进度值越来越大，越来越小的一个动态效果
			if (stateChange == false) {
				if (current >= currentMax) {
					stateChange = true;
				} else {
					// 设置进度值
					pb.setProgress(current + 1);
					// 设置底层进度值
					pb.setSecondaryProgress(secCurrent + 1);
				}
			} else {
				if (current <= 0) {
					stateChange = false;
				} else {
					pb.setProgress(current - 1);
				}
			}
			try {
				Thread.sleep(50);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}
```