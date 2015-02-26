在Android中通过文件的MIME类型来判断有哪些应用程序可以处理这些文件，并使用其中的某一个应用程序(如果有多个可选的应用程序，则用户必须指定一个)处理之。
我在写android资源管理器(文件浏览器)的时候，希望能在资源管理器的中实现打开文件的操作，此时就需要用到文件的MIME类型。
实现方法：
```  
 /**
  * 根据文件后缀名获得对应的MIME类型。
  * @param file
  */
private String getMIMEType(File file) {
	String type = "*/*";
	String fName = file.getName();
	// 获取后缀名前的分隔符"."在fName中的位置。
	int dotIndex = fName.lastIndexOf(".");

	if (dotIndex < 0) {
		return type;
	}
	/* 获取文件的后缀名 */
	String end = fName.substring(dotIndex, fName.length()).toLowerCase();
	if (end == "")
		return type;
	// 在MIME和文件类型的匹配表中找到对应的MIME类型。
	for (int i = 0; i < p; i++) {
		if (end.equals(MIME_MapTable[i][0]))
			type = MIME_MapTable[i][1];
	}
	return type;
}
 /**
  * 打开文件
  * @param file
  */
private void openFile(File file) {
	// Uri uri = Uri.parse("file://"+file.getAbsolutePath());
	Intent intent = new Intent();
	intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	// 设置intent的Action属性
	intent.setAction(Intent.ACTION_VIEW);
	// 获取文件file的MIME类型
	String type = getMIMEType(file);
	// 设置intent的data和Type属性。
	intent.setDataAndType(/* uri */Uri.fromFile(file), type);
	// 跳转
	startActivity(intent);
}
```
现在就差一个MIME类型和文件类型的匹配表了。
"文件类型——MIME类型"的匹配表:
建立一个MIME类型与文件后缀名的匹配表
java代码：
```  
private final String[][] MIME_MapTable = {
	// {后缀名， MIME类型}
	{ ".3gp", "video/3gpp" },
	{ ".apk", "application/vnd.android.package-archive" },
	{ ".asf", "video/x-ms-asf" }, { ".avi", "video/x-msvideo" },
	{ ".bin", "application/octet-stream" }, { ".bmp", "image/bmp" },
	{ ".c", "text/plain" }, { ".class", "application/octet-stream" },
	{ ".conf", "text/plain" }, { ".cpp", "text/plain" },
	{ ".doc", "application/msword" },
	{ ".exe", "application/octet-stream" }, { ".gif", "image/gif" },
	{ ".gtar", "application/x-gtar" }, { ".gz", "application/x-gzip" },
	{ ".h", "text/plain" }, { ".htm", "text/html" },
	{ ".html", "text/html" }, { ".jar", "application/java-archive" },
	{ ".java", "text/plain" }, { ".jpeg", "image/jpeg" },
	{ ".jpg", "image/jpeg" }, { ".js", "application/x-javascript" },
	{ ".log", "text/plain" }, { ".m3u", "audio/x-mpegurl" },
	{ ".m4a", "audio/mp4a-latm" }, { ".m4b", "audio/mp4a-latm" },
	{ ".m4p", "audio/mp4a-latm" }, { ".m4u", "video/vnd.mpegurl" },
	{ ".m4v", "video/x-m4v" }, { ".mov", "video/quicktime" },
	{ ".mp2", "audio/x-mpeg" }, { ".mp3", "audio/x-mpeg" },
	{ ".mp4", "video/mp4" },
	{ ".mpc", "application/vnd.mpohun.certificate" },
	{ ".mpe", "video/mpeg" }, { ".mpeg", "video/mpeg" },
	{ ".mpg", "video/mpeg" }, { ".mpg4", "video/mp4" },
	{ ".mpga", "audio/mpeg" },
	{ ".msg", "application/vnd.ms-outlook" }, { ".ogg", "audio/ogg" },
	{ ".pdf", "application/pdf" }, { ".png", "image/png" },
	{ ".pps", "application/vnd.ms-powerpoint" },
	{ ".ppt", "application/vnd.ms-powerpoint" },
	{ ".prop", "text/plain" },
	{ ".rar", "application/x-rar-compressed" },
	{ ".rc", "text/plain" }, { ".rmvb", "audio/x-pn-realaudio" },
	{ ".rtf", "application/rtf" }, { ".sh", "text/plain" },
	{ ".tar", "application/x-tar" },
	{ ".tgz", "application/x-compressed" }, { ".txt", "text/plain" },
	{ ".wav", "audio/x-wav" }, { ".wma", "audio/x-ms-wma" },
	{ ".wmv", "audio/x-ms-wmv" },
	{ ".wps", "application/vnd.ms-works" },
	// {".xml", "text/xml"},
	{ ".xml", "text/plain" }, { ".z", "application/x-compress" },
	{ ".zip", "application/zip" }, { "", "*/*" }
};
```