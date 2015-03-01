代码如下：
```  
public void onCreate() {
	downThread = new Thread(this); // 初始化下载线程
	downThread.start();
}
@Override
public void onStart(Intent intent, int startId) {
	URL_str = intent.getExtras().getString("url"); // 获取下载链接的url
	musicName = intent.getExtras().getString("musicName");// 获取下载的文件名
}
@Override
public void onDestroy() {
	flag = true; // 停止下载
}
// 实现Run方法，进行歌曲的下载
@Override
public void run() {
	FileOutputStream fos = null; // 文件输出流
	FileInputStream fis = null; // 文件输出流
	InputStream is = null; // 网络文件输入流
	URL url = null;
	try {
		url = new URL(URL_str); // 网络歌曲的url
		HttpURLConnection httpConnection = null;
		httpConnection = (HttpURLConnection) url.openConnection(); // 打开网络连接
		download_file = new File(Environment. // 在sdcard根目录建立一个与要下载的文件的名字相同的文件
				getExternalStorageDirectory() + "/" + musicName);

		fos = new FileOutputStream(download_file, true); // 初始化文件输出流
		fis = new FileInputStream(download_file); // 初始化文件输入流
		total_read = fis.available(); // 初始化“已下载部分”的长度，此处应为0
		music_length = httpConnection.getContentLength(); // 要下载的文件的总长度
		if (is == null) { // 如果下载失败则打印日志，并返回
			Log.i("info", "donload failed...");
			return;
		}
		byte buf[] = new byte[1024]; // 定义下载缓冲区
		readLength = 0; // 一次性下载的长度
		Log.i("info", "download start...");
		// 向前台发送开始下载广播
		Intent startIntent = new Intent();
		startIntent.setAction("com.alex.downloadstart");
		sendBroadcast(startIntent);
		// 如果读取网络文件的数据流成功，且用户没有选择停止下载，则开始下载文件
		while (readLength != -1 && !flag) {
			if ((readLength = is.read(buf)) > 0) {
				fos.write(buf, 0, readLength);
				total_read += readLength; // 已下载文件的长度增加
			}
			if (total_read == music_length) { // 当已下载的长度等于网络文件的长度，则下载完成
				flag = false;
				Log.i("info", "download complete...");
				// 向前台发送下载完成广播
				Intent completeIntent = new Intent();
				completeIntent.setAction("com.alex.downloadcompleted");
				sendBroadcast(completeIntent);
				// 关闭输入输出流
				fos.close();
				is.close();
				fis.close();
			}
			Thread.sleep(50); // 当前现在休眠50毫秒
			Log.i("info",
					"download process : " // 打印下载进度
							+ ((total_read + 0.0) / music_length * 100 + "")
									.substring(0, 4) + "%");
		}
	} catch (Exception e) {
		Intent errorIntent = new Intent();
		errorIntent.setAction("com.alex.downloaderror");
		sendBroadcast(errorIntent);
		e.printStackTrace();
	}
}
```