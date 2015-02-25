很多的人可能会对android的安装和下载不太了解，下面我就来给大家做个介绍：
代码如下：
安装：从sdcard
```  
String fileName = Environment.getExternalStorageDirectory() + "/myApp.apk";
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setDataAndType(Uri.parse("file：//" + filePath)，"application/vnd.android.package-archive");
```
或者
```  
//intent.setDataAndType(Uri.fromFile(new File(fileName))， "application/vnd.android.package-archive");
startActivity(intent);
```
安装或升级 从网络
```  
Intent intent = new Intent();
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.setAction(android.content.Intent.ACTION_VIEW);
/* 调用getMIMEType()来取得MimeType */
String type = getMIMEType(f);
/* 设置intent的file与MimeType */
intent.setDataAndType(Uri.fromFile(f)，type);
startActivity(intent);
```
需要的权限
```  
<uses-permission android：name="android.permission.INTERNET"></uses-permission>
<uses-permission android：name="android.permission.INSTALL_PACKAGES"></uses-permission>
<uses-permission android：name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"></uses-permission>
<uses-permission android：name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
```
卸载
```  
Uri packageURI = Uri.parse("package：com.android.myapp");
Intent uninstallIntent = new Intent(Intent.ACTION_DELETE，packageURI);
startActivity(uninstallIntent);
```
