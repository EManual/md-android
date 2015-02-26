如下图所示，通过progressBar来展示当前的sdcard容量。下面我们就来看看怎么样用代码来实现这个比较不错的小实例吧。其中重要的东西都有注释，希望对大家有帮助。
![img](P)  
代码如下：
```  
import java.io.File;
import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;
import android.os.StatFs;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.ProgressBar;
import android.widget.Toast;
public class SDCardActivity extends Activity implements OnClickListener {
	String result = "SDCard容量相关信息：/n";
	ProgressBar progressBar;
	Button button;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.sdcard);
		progressBar = (ProgressBar) findViewById(R.id.progressBar_sdcard);
		button = (Button) findViewById(R.id.button_sdcard);
		button.setOnClickListener(this);
	}
	private void showSDCardSize() {
		progressBar.setProgress(0);
		File sdcard = Environment.getExternalStorageDirectory();
		 /**
		  * 我们可以通过StatFs访问文件系统的空间容量等信息
		  */
		StatFs statFs = new StatFs(sdcard.getPath());
		 /**
		  * statFs.getBlockSize可以获取当前的文件系统中，一个block所占有的字节数
		  */
		int blockSize = statFs.getBlockSize();
		 /**
		  * statFs.getAvaliableBlocks方法可以返回尚未使用的block的数量
		  */
		int avaliableBlocks = statFs.getAvailableBlocks();
		 /**
		  * statFs.getBlockCount可以获取总的block数量
		  */
		int totalBlocks = statFs.getBlockCount();
		result += "/n 尚未被使用的空间大小：" + avaliableBlocks * blockSize + "byte";
		result += "/n 总空间大小：" + totalBlocks * blockSize + "byte";
		float a = (float) avaliableBlocks / totalBlocks;
		int b = Integer.valueOf(Float.valueOf(a * 100).toString()
				.substring(0, 2));
		progressBar.setProgress(90);
		Log.i("通知", result);
		Toast.makeText(this, b + " " + result, Toast.LENGTH_LONG).show();
	}
	@Override
	public void onClick(View v) {
		showSDCardSize();
	}
}
```
XML文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!--
		progressBar设置为水平的长框而不是一直旋转的小圆圈，应该通过如下语句设置
		style="?android:attr/progressBarStyleHorizontal"
		或者
		style="?android:progressBarStyleHorizontal"
    -->
    <ProgressBar
        android:id="@+id/progressBar_sdcard"
        style="?android:progressBarStyleHorizontal"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        mce_style="?android:progressBarStyleHorizontal"
        android:max="100"
        android:progress="0" >
    </ProgressBar>
    <Button
        android:id="@+id/button_sdcard"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button" >
    </Button>
</LinearLayout>
```