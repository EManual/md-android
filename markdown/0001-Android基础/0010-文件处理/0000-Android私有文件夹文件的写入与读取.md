在介绍如何在Android平台下进行文件的读取之前，有必要了解Android平台下的数据存储规则。在其他的操作系统如Windows平台下，应用程序可以自由地或者在特定的访问权限基础上访问或修改其他应用程序名下的文件等资源，而在Android平台下，一个应用程序中所有的数据都是私有的。
当应用程序被安装到系统中后，其所在的包会有一个文件夹用于存放自己的数据，只有这个应用程序才有对这个文件夹的写入权限，这个私有的文件夹位于Android系统的/data/data/<应用程序包名>目录下，其他的应用程序都无法再这个文件夹中写入数据。除了存放私有的数据文件夹外，应用程序也具有SD卡的写入权限。
使用文件I/O方法可以直接往手机中存储数据，默认情况下这些文件不可以被其他的应用程序访问。Android平台支持java平台下的文件I/O操作，主要使用FileInputStream 和 FileOutputStream 这两个类来实现文件的存储与读取。获取这两个类对象的方式有两种。
一：第一种方式就是像Java平台下的实现方式一样通过构造器直接创建，如果需要向打开的文件末尾写入数据，可以通过使用构造器FileOutputStream(File file, boolean append)将 append设置为true来实现。不过需要注意的是采用这种方式获得FileOutputStream 对象时如果文件不存在或不可写入时，会抛出 FileNotFoundException 异常。
二：第二种获取 FileInputStream 和 FileOutputStream 对象的方式是调用 Context.openFileInput 和 Context.openFileOutput两个方法来创建。除了这两个方法外，Context对象还提供了其他几个用于对文件操作的方法。
下面通过一个小例子来说明Android平台下的文件I/O 操作方式，主要功能是在应用程序私有的数据文件夹下创建一个文件并读取其中的数据显示到屏幕的 TextView中,这个例子也比较简单只有一个类。
先看一下运行后的效果吧。
![img](P)  
```  
import java.io.FileInputStream;
import java.io.FileOutputStream;
import org.apache.http.util.EncodingUtils;
import android.R;
import android.app.Activity;
import android.graphics.Color;
import android.os.Bundle;
import android.widget.TextView;
public class Activity01 extends Activity {
	// 常量，为编码格式
	public static final String ENCODING = "UTF-8";
	// 定义文件的名称
	String fileName = "test.txt";
	// 写入和读出的数据信息
	String message = "欢迎大家来我们群里讨论问题";
	TextView textView;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		writeFileData(fileName, message);
		String result = readFileData(fileName);
		textView = (TextView) findViewById(R.id.tv);
		textView.setTextColor(Color.GREEN);
		textView.setTextSize(20.0f);
		textView.setText(result);
	}
	// 向指定的文件中写入指定的数据
	public void writeFileData(String filename, String message) {
		try {
			FileOutputStream fout = openFileOutput(filename, MODE_PRIVATE);// 获得FileOutputStream
			// 将要写入的字符串转换为byte数组
			byte[] bytes = message.getBytes();
			fout.write(bytes);// 将byte数组写入文件
			fout.close();// 关闭文件输出流
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	// 打开指定文件，读取其数据，返回字符串对象
	public String readFileData(String fileName) {
		String result = "";
		try {
			FileInputStream fin = openFileInput(fileName);
			// 获取文件长度
			int lenght = fin.available();
			byte[] buffer = new byte[lenght];
			fin.read(buffer);
			// 将byte数组转换成指定格式的字符串
			result = EncodingUtils.getString(buffer, ENCODING);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return result;
	}
}
```
希望这个能对大家有点帮助。