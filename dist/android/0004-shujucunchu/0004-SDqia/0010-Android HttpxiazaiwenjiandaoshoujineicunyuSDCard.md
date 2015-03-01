Android网络应用的开发，http的下载是常用到的功能，因此开发了一个工具类供使用，提供了从网络上下载文件到手机内存和SDCard的功能。主要包括下载网络文件（包括文本文件和音频文件）存储到本地和下载文本文件获取内容显示。希望能对大家有用。先对工具类做些简要的介绍。
访问Internet和保存文件到SDCard上，首先要在mainifest.xml文件中加上下面的权限。
```  
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
```
在工具类中提供了一个 构造函数，其中需要传递Context参数，有助于现实提示信息：
```  
public PTTJDownLoadUtil(Context c) {
	if (c != null) {
		this.c = c;
	} else {
		Log.w(ERROTAG, "上下文为空");
	}
}
```
gettextfilestring(String url)获取文本文件内容：
```  
public String gettextfilestring(String url) {
	InputStream input = getinputStream(url);
	StringBuffer sb = new StringBuffer("");
	BufferedReader bfr = new BufferedReader(new InputStreamReader(input));
	String line = "";
	try {
		while ((line = bfr.readLine()) != null) {
			sb.append(line);
		}
	} catch (IOException e) {
		toasterror("流文件读写错误");
		e.printStackTrace();
	} finally {
		try {
			bfr.close();
		} catch (IOException e) {
			toasterror("流文件未能正常关闭");
			e.printStackTrace();
		}
	}
	return sb.toString();
}
```
downFiletoDecive(String url,String filename)方法下载文件到设备内存，下载的文件在应用的路径file下：
```  
public void downFiletoDecive(String url, String filename) {
	if ((url != null && !"".equals(url))
			&& (filename != null && !"".equals(filename))) {
		InputStream input = getinputStream(url);
		FileOutputStream outStream = null;
		try {
			outStream = c.openFileOutput(filename,
					Context.MODE_WORLD_READABLE
							| Context.MODE_WORLD_WRITEABLE);
			int temp = 0;
			byte[] data = new byte[1024];
			while ((temp = input.read(data)) != -1) {
				outStream.write(data, 0, temp);
			}
		} catch (FileNotFoundException e) {
			toasterror("请传入正确的上下文");
			e.printStackTrace();
		} catch (IOException e) {
			toastemessage("读写错误");
			e.printStackTrace();
		} finally {
			try {
				outStream.flush();
				outStream.close();
			} catch (IOException e) {
				toasterror("流文件未能正常关闭");
				e.printStackTrace();
			}
		}
	}
	toastemessage("下载成功");
}
```
downFiletoSDCard(String url,String path,String filename)下载文件到SDCard中，自定义保存路径：
```  
public void downFiletoSDCard(String url, String path, String filename) {
	if ((url != null && !"".equals(url)) && (path != null)
			&& (filename != null && !"".equals(filename))) {
		InputStream input = getinputStream(url);
		downloader(input, path, filename);
	} else {
		/*
		  * 对不合发的参数做处理
		  */
		if (url == null || "".equals(url)) {
			toasterror("url不能为空或为“”");
		}
		if (path == null) {
			toasterror("path不能为空");
		}
		if (filename == null || "".equals(filename)) {
			toasterror("filename不能为空");
		}
	}
}       
```
