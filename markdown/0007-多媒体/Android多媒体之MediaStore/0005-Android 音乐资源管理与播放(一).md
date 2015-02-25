MediaScanner与音乐信息扫描
Android系统在SD卡插入后，MediaScanner服务会在后台自动扫描SD上的文件资源，将SD上的音乐媒体信息加入到MediaStore数据库中。程序可以直接从MediaStore中读取相应的媒体信息。通过注册监听MediaScanner广播的Intent，可以获知MediaScanner服务是否在进行后台的扫描工作：
```  
Intent.ACTION_MEDIA_SCANNER_STARTED 表示MeidaScanner开始扫描；
Intent.ACTION_MEDIA_SCANNER_FINISHED 表示MediaScanner扫描结束；
```
当程序从网络下载媒体文件到终端后，MediaScanner服务并不会自动扫描刚刚下载的文件，需要程序主动去扫描这些新添加的媒体文件信息到MediaStore数据库中。在Android系统中有两种方式去主动扫描音乐媒体文件信息到
MediaStore数据库：
1．启动MediaScanner服务，扫描媒体文件：
程序通过发送下面的Intent启动MediaScanner服务扫描指定的文件或目录：
Intent.ACTION_MEDIA_SCANNER_SCAN_FILE：扫描指定文件
```  
public void scanFileAsync(Context ctx, String filePath) {
	Intent scanIntent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
	scanIntent.setData(Uri.fromFile(new File(filePath)));
	ctx.sendBroadcast(scanIntent);
}
```
"android.intent.action.MEDIA_SCANNER_SCAN_DIR"：扫描指定目录
```  
public static final String ACTION_MEDIA_SCANNER_SCAN_DIR = "android.intent.action.MEDIA_SCANNER_SCAN_DIR";
public void scanDirAsync(Context ctx, String dir) {
	Intent scanIntent = new Intent(ACTION_MEDIA_SCANNER_SCAN_DIR);
	scanIntent.setData(Uri.fromFile(new File(dir)));
	ctx.sendBroadcast(scanIntent);
}
```
这种扫描方式中，由于扫描工作是在MediaScanner服务中进行的，因此不会阻塞当前程序进程。当扫描大量媒体文件且实时性要求不高的情况下，适合使用该扫描方式。
2．通过MediaScanner提供的API接口，扫描媒体文件。
这种扫描媒体文件的方式是同步的，扫描工作将会阻塞当前的程序进程。当扫描少量文件，且要求立即获取扫描结果的情况下，适合使用该扫描方式。
在扫描媒体文件前，程序应该根据终端当前的语言环境正确设置MediaScanner的语言环境设置, 避免产生编解码的错误:
```  
MediaScanner scanner = new MediaScanner(ctx);
Locale locale = ctx.getResources().getConfiguration().locale;
String language = locale.getLanguage();
String country = locale.getCountry();
scanner.setLocale(language + \"_\" + country); 
```
媒体文件可以存储在手机终端的内存中，也可以存储在SD卡中，Android平台中称手机终端内存为内部存储空间，称SD卡为外部存储空间。针对内部和外部存储空间中的媒体文件信息是分开管理的，各自有独立的数据库管理。因此在扫描媒体文件时，要明确指明扫描的媒体文件是位于内部存储空间还是外部存储空间。外部存储空间和内部存储空间对应的卷标为”external”和”internal”。
```  
scanner.scanSingleFile(filePath, volumeName, mimeType);
scanner.scanDirectories(directories, volumeName); 
```