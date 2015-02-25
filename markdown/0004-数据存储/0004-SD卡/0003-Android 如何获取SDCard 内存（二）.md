#### 2、讲述 StatFs 类 
StatFs 一个模拟linux的df命令的一个类，获得SD卡和手机内存的使用情况 
StatFs 常用方法: 
```  
getAvailableBlocks() 
解释：返回 Int，获取当前可用的存储空间 
getBlockCount() 
解释：返回 Int，获取该区域可用的文件系统数 
getBlockSize() 
解释：返回 Int，大小，以字节为单位，一个文件系统 
getFreeBlocks() 
解释：返回 Int，该块区域剩余的空间 
restat(String path) 
解释：执行一个由该对象所引用的文件系统 
```
3、完整例子读取 SDCard 内存 
存储卡在 Android 手机上是可以随时插拔的，每次的动作都对引起操作系统进行 ACTION_BROADCAST，本例子将使用上面学到的方法，计算出 SDCard 的剩余容量和总容量。代码如下： 
```  
import java.io.File;
import java.text.DecimalFormat;
import android.R;
import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;
import android.os.StatFs;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.ProgressBar;
import android.widget.TextView;
import android.widget.Toast;
public class getStorageActivity extends Activity {
	private Button myButton;
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		findView();
		viewHolder.myButton.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View arg0) {
				getSize();
			}
		});
	}
	void findView() {
		viewHolder.myButton = (Button) findViewById(R.id.Button01);
		viewHolder.myBar = (ProgressBar) findViewById(R.id.myProgressBar);
		viewHolder.myTextView = (TextView) findViewById(R.id.myTextView);
	}
	void getSize() {
		viewHolder.myTextView.setText("");
		viewHolder.myBar.setProgress(0);
		// 判断是否有插入存储卡
		if (Environment.getExternalStorageState().equals(
				Environment.MEDIA_MOUNTED)) {
			File path = Environment.getExternalStorageDirectory();
			// 取得sdcard文件路径
			StatFs statfs = new StatFs(path.getPath());
			// 获取block的SIZE
			long blocSize = statfs.getBlockSize();
			// 获取BLOCK数量
			long totalBlocks = statfs.getBlockCount();
			// 己使用的Block的数量
			long availaBlock = statfs.getAvailableBlocks();
			String[] total = filesize(totalBlocks * blocSize);
			String[] availale = filesize(availaBlock * blocSize);
			// 设置进度条的最大值
			int maxValue = Integer.parseInt(availale[0]);
			viewHolder.myBar.setProgress(maxValue);
			String Text = "总共：" + total[0] + total[1] + "\n" + "可用:
					+ availale[0] + availale[1];
			viewHolder.myTextView.setText(Text);
		} else if (Environment.getExternalStorageState().equals(
				Environment.MEDIA_REMOVED)) {
			Toast.makeText(getStorageActivity.this, "没有sdCard", 1000).show();
		}
	} // 返回数组，下标1代表大小，下标2代表单位 KB/MB
	String[] filesize(long size) {
		String str = "";
		if (size >= 1024) {
			str = "KB";
			size /= 1024;
			if (size >= 1024) {
				str = "MB";
				size /= 1024;
			}
		}
		DecimalFormat formatter = new DecimalFormat();
		formatter.setGroupingSize(3);
		String result[] = new String[2];
		result[0] = formatter.format(size);
		result[1] = str;
		return result;
	}
}
```