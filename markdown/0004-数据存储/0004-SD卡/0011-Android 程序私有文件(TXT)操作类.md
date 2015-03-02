程序私有文件顾名思义就是本程序可以访问，其他程序无权访问的一类文件。    
众所周知Android有一套自己的安全模型，具体可参见Android开发文档。当应用程序(.apk)在安装时就会分配一个userid，当该应用要去访问其他资源比如文件的时候，就需要userid匹配。默认情况下，任何应用创建的文件，数据库，sharedpreferences都应该是私有的（位于/data/data/your_project/files/），其余程序无法访问。除非在创建时指明是MODE_WORLD_READABLE或者MODE_WORLD_WRITEABLE,只要这样其余程序才能正确访问。
目录结构如下：

![img](http://emanual.github.io/md-android/img/data_sdcard/12_sdcard.jpg) 

使用Context.openFileOutput()和Context.openFileInput()来访问文件。
程序开发或者游戏开发过程中经常使用到文件存储数据，为了简化开发，自己封装了一个类。
```  
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import android.content.Context;
 /**
  * Android私有TXT文件操作类
  */
public class TextManager {
	private Context context;
	private String fileName;
	 /**
	  * 构造函数
	  * 
	  * @param context
	  * @param fileName
	  */
	public TextManager(Context context, String fileName) {
		this.context = context;
		this.fileName = fileName;
	}
	 /**
	  * 判断文件是否存在
	  * 
	  * @return
	  */
	public boolean isExist() {
		String[] fileNameArray = context.fileList();
		for (int i = 0; i < fileNameArray.length; i++) {
			if (fileNameArray[i].equals(fileName)) {
				return true;
			}
		}
		return false;
	}
	 /**
	  * 创建文件
	  * 
	  * @return
	  */
	public boolean create() {
		boolean result = false;
		if (!isExist()) {
			FileOutputStream fos = null;
			try {
				fos = context.openFileOutput(fileName, Context.MODE_PRIVATE);
			} catch (FileNotFoundException e) {
				e.printStackTrace();
			}
			if (fos != null) {
				result = true;
				try {
					fos.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}
		return result;
	}
	 /**
	  * 写入
	  * 
	  * @param str
	  * @return
	  */
	public boolean write(String str) {
		boolean result = true;
		FileOutputStream fos = null;
		if (isExist()) {
			try {
				fos = context.openFileOutput(fileName, Context.MODE_APPEND);
			} catch (FileNotFoundException e) {
				e.printStackTrace();
				result = false;
			}
			byte bt[] = (str + "\n").getBytes();
			if (fos != null) {
				try {
					fos.write(bt);
				} catch (IOException e) {
					e.printStackTrace();
					result = false;
				}
				try {
					fos.close();
				} catch (Exception e) {
					e.printStackTrace();
					result = false;
				}
			}
		}
		return result;
	}
	 /**
	  * 读取一行
	  * 
	  * @return
	  */
	public String readLine() {
		String str = "";
		FileInputStream fis = null;
		if (isExist()) {
			try {
				fis = context.openFileInput(fileName);
			} catch (FileNotFoundException e) {
				e.printStackTrace();
			}
			BufferedReader br = new BufferedReader(new InputStreamReader(fis));
			try {
				str = br.readLine();
				fis.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return str;
	}
	 /**
	  * 读取全部
	  * 
	  * @return
	  */
	public String read() {
		String str = "";
		String line = "";
		FileInputStream fis = null;
		if (isExist()) {
			try {
				fis = context.openFileInput(fileName);
			} catch (FileNotFoundException e) {
				e.printStackTrace();
			}
			BufferedReader br = new BufferedReader(new InputStreamReader(fis));
			try {
				while ((line = br.readLine()) != null) {
					str += line;
				}
				fis.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return str;
	}
	 /**
	  * 清空文件
	  */
	public void clean() {
		delete();
		create();
	}
	 /**
	  * 删除文件
	  */
	public void delete() {
		context.deleteFile(fileName);
	}
}
```
