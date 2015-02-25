1.手机信息查看助手可行性分析
开始进入编写程序前，需要对需求的功能做一些可行性分析，以做到有的放矢，如果有些无法实现的功能，可以尽快调整。
这里分析一下项目需要的功能，主要是信息查看和信息收集，如版本信息、硬件信息等，这些都可以通过读取系统文件或者运行系统命令获取，而像获取安装的软件信息和运行时信息则需要通过API提供的接口获取。实现API接口不是什么问题，主要把精力集中在如何实现运行系统命令，获取其返回的结果功能实现上。具体实现代码如下所示：
```  
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
public class CMDExecute {
	public synchronized String run(String[] cmd, String workdirectory)
			throws IOException {
		String result = "";
		try {
			ProcessBuilder builder = new ProcessBuilder(cmd);
			InputStream in = null;
			// 设置一个路径
			if (workdirectory != null) {
				builder.directory(new File(workdirectory));
				builder.redirectErrorStream(true);
				Process process = builder.start();
				in = process.getInputStream();
				byte[] re = new byte[1024];

				while (in.read(re) != -1)
					result = result + new String(re);
			}
			if (in != null) {
				in.close();
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		}
		return result;
	}
}
```
1.2 手机信息查看助手功能实现
1.2.1 手机信息查看助手主界面
按照预设的规划，将4类信息的查看入口放在主界面上，其布局文件为main.xml，基本上是用一个列表组件组成的，实现代码如下所示：
在这里main.xml中使用的是LinearLayout布局，其中放置了一个ListView组件。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ListView
        android:id="@+id/itemlist"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```
1.2.2 查看系统信息实现
当在运行的主界面单击第一行时，也就是“系统信息”这一行，将执行代码如下：
```  
case 0:
intent.setClass(eoeInfosAssistant.this, System.class);
startActivity(intent);
break;
```
代码运行后将显示系统(System)这个界面，这就是查看系统信息的主界面，其和主界面差不多，也就是列表显示几个需要查看的系统信息。
1.2.2.1 操作系统版本
单击图界面第一行“操作系统版本”项，则会打开一个新的界面，其对应的是ShowInfo.java文件，然后需要显示该设备的操作系统版本信息，而这个信息在/proc/version中有，可以直接调用。在可行性分析中给出的CMDExencute类来调用系统的cat命令获取该文件的内容，实现代码如下：
```  
public static String fetch_version_info() {
	String result = null;
	CMDExecute cmdexe = new CMDExecute();
	try {
		String[] args = { "/system/bin/cat", "/proc/version" };
		result = cmdexe.run(args, "system/bin/");
	} catch (IOException ex) {
		ex.printStackTrace();
	}
	return result;
}
```
上述代码使用的是CMDExecute类，调用系统的“"/system/bin/cat"”工具，获取“"/proc/version"”中内容。