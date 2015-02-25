开始存储路径写死为：private String folder = "/sdcard/DCIM/Camera/"（SD卡上拍照程序的图片存储路径）; 后来发现这样写虽然一般不会出错，但不是很好，因为不同相机，可能路径会出问题。较好的方法是通过Environment 来获取路径，最后给出一个例子，教你怎样获取SDCard 的内存，显示出来告诉用户。讲述的内容如下： 
```  
0、获取sd卡路径。 
1、讲述 Environment 类。 
2、讲述 StatFs 类。 
3、完整例子读取 SDCard 内存 
```
#### 0、获取sd卡路径 
方法一： private String folder = "/sdcard/DCIM/Camera/"（SD卡上拍照程序的图片存储路径）; //写死绝对路径，不赞成使用 
java代码：
```  
public String getSDPath(){   
	File sdDir = null;
	boolean sdCardExist = Environment.getExternalStorageState().equals(android.os.Environment.MEDIA_MOUNTED);  //判断sd卡是否存在   
	if(sdCardExist) {
		sdDir = Environment.getExternalStorageDirectory();//获取跟目录
	} 
	return sdDir.toString();
}
```
然后：在后面加上斜杠，在加上文件名
```  
String fileName = getSDPath() +"/" + name;//以name存在目录中 
```
1、讲述 Environment 类 
Environment 是一个提供访问环境变量的类。 
Environment 包含常量：
```   
MEDIA_BAD_REMOVAL 
解释：返回getExternalStorageState()，表明SDCard 被卸载前己被移除 
MEDIA_CHECKING 
解释：返回getExternalStorageState()，表明对象正在磁盘检查。 
MEDIA_MOUNTED 
解释：返回getExternalStorageState()，表明对象是否存在并具有读/写权限 
MEDIA_MOUNTED_READ_ONLY 
解释：返回getExternalStorageState()，表明对象权限为只读 
MEDIA_NOFS 
解释：返回getExternalStorageState()，表明对象为空白或正在使用不受支持的文件系统。 
MEDIA_REMOVED 
解释：返回getExternalStorageState()，如果不存在 SDCard 返回 
MEDIA_SHARED 
解释：返回getExternalStorageState()，如果 SDCard 未安装 ，并通过 USB 大容量存储共享 返回 
MEDIA_UNMOUNTABLE 
解释：返回getExternalStorageState()，返回 SDCard 不可被安装 如果 SDCard 是存在但不可以被安装 
MEDIA_UNMOUNTED 
解释：返回getExternalStorageState()，返回 SDCard 已卸掉如果 SDCard  是存在但是没有被安装 
```
Environment 常用方法： 
```   
方法：getDataDirectory() 
解释：返回 File ，获取 Android 数据目录。 
方法：getDownloadCacheDirectory() 
解释：返回 File ，获取 Android 下载/缓存内容目录。 
方法：getExternalStorageDirectory() 
解释：返回 File ，获取外部存储目录即 SDCard 
方法：getExternalStoragePublicDirectory(String type) 
解释：返回 File ，取一个高端的公用的外部存储器目录来摆放某些类型的文件 
方法：getExternalStorageState() 
解释：返回 File ，获取外部存储设备的当前状态 
方法：getRootDirectory() 
解释：返回 File ，获取 Android 的根目录 
```