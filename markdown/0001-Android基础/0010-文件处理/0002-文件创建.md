代码如下：
```  
import java.io.File;
import java.io.IOException;
import android.R;
import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;
public class FileProForAndroidActivity extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		String fileName = "myFristData.txt";
		// 注意一个问题：斜杠的方向和windows的是相反的（java中是除号）
		// D:/java与D:/java/效果是一样的。
		String filePath = Environment.getExternalStorageDirectory() + "/";
		File file = new File(filePath, fileName);
		// 创建路径
		file.mkdirs();
		try {
			if (!file.exists())
				file.createNewFile();
		} catch (IOException e) {
			e.printStackTrace();
		}
		System.out.println(filePath);
		// 这里有必要说明： File file=new File(filePath,
		// fileName)不会创建文件，而是创建了一个myFristData.txt的文件夹。
		// File file=new File(filePath+ fileName)才会创建一个文件(Xp）在win7下仍不成功。
	}
}
```