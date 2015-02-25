获取内存信息的主要代码
```  
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.io.IOException;
import java.util.HashMap;
import android.content.Context;
import android.os.Environment;
import android.os.StatFs;
import android.text.format.Formatter;
import android.util.Log;
public class SystemMemoryInfo {
	public static HashMap<String, String> getMemoryInfo(Context context) {
		HashMap<String, String> hmMeminfo = new HashMap<String, String>();
		// 系统内存信息文件
		String meminfoPath = "/proc/meminfo";
		BufferedReader br = null;
		try {
			FileReader fr = new FileReader(meminfoPath);
			br = new BufferedReader(fr, 4096);
			String lineStr = null;
			while ((lineStr = br.readLine()) != null) {
				// 空格区分 分为三部分
				// 1. 属性名： 2.数值 3.kB
				// eg. MemTotal: 126608 kB
				String[] lineItems = lineStr.split("\\s+");
				if (lineItems != null && lineItems.length == 3) {
					// 去掉[:]
					String itemName = lineItems[0].substring(0,
							lineItems[0].length() - 1);
					Log.i("itemName", itemName);
					// 单位是KB，乘以1024转换为Byte,
					long itemMemory = Integer.valueOf(lineItems[1]) * 1024;
					// Byte转换为KB或者MB，内存大小规格化
					String itemValue = Formatter.formatFileSize(context,
							itemMemory);
					hmMeminfo.put(itemName, itemValue);
				}
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			if (br != null) {
				try {
					br.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}
		// 打印MemoryInfo信息
		Log.i("getMemoryInfo", hmMeminfo.toString());
		return hmMeminfo;
	}
	static public long getAvailableInternalMemorySize() {
		File path = Environment.getDataDirectory();
		StatFs stat = new StatFs(path.getPath());
		long blockSize = stat.getBlockSize();
		long availableBlocks = stat.getAvailableBlocks();
		return availableBlocks * blockSize;
	}
	// 这个是手机内存的总空间大小
	public static long getTotalInternalMemorySize() {
		File path = Environment.getDataDirectory();
		StatFs stat = new StatFs(path.getPath());
		long blockSize = stat.getBlockSize();
		long totalBlocks = stat.getBlockCount();
		return totalBlocks * blockSize;
	}
}
```