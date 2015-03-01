Android.os下的StatFs类主要用来获取文件系统的状态，能够获取sd卡的大小和剩余空间，获取系统内部空间也就是/system的大小和剩余空间等等。
看下读取sd卡的：
```  
void readSDCard() {
	String state = Environment.getExternalStorageState();
	if (Environment.MEDIA_MOUNTED.equals(state)) {
		File sdcardDir = Environment.getExternalStorageDirectory();
		StatFs sf = new StatFs(sdcardDir.getPath());
		long blockSize = sf.getBlockSize();
		long blockCount = sf.getBlockCount();
		long availCount = sf.getAvailableBlocks();
		Log.d("", "block大小:" + blockSize + ",block数目:" + blockCount
				+ ",总大小:" + blockSize * blockCount / 1024 + "KB");
		Log.d("", "可用的block数目：:" + availCount + ",剩余空间:" + availCount
				 * blockSize / 1024 + "KB");
	}
}
void readSDCard() {
	String state = Environment.getExternalStorageState();
	if (Environment.MEDIA_MOUNTED.equals(state)) {
		File sdcardDir = Environment.getExternalStorageDirectory();
		StatFs sf = new StatFs(sdcardDir.getPath());
		long blockSize = sf.getBlockSize();
		long blockCount = sf.getBlockCount();
		long availCount = sf.getAvailableBlocks();
		Log.d("", "block大小:" + blockSize + ",block数目:" + blockCount
				+ ",总大小:" + blockSize * blockCount / 1024 + "KB");
		Log.d("", "可用的block数目：:" + availCount + ",剩余空间:" + availCount
				 * blockSize / 1024 + "KB");
	}
}
```
然后看下读取系统内部空间的：
```  
void readSystem() {
	File root = Environment.getRootDirectory();
	StatFs sf = new StatFs(root.getPath());
	long blockSize = sf.getBlockSize();
	long blockCount = sf.getBlockCount();
	long availCount = sf.getAvailableBlocks();
	Log.d("", "block大小:" + blockSize + ",block数目:" + blockCount + ",总大小:
			+ blockSize * blockCount / 1024 + "KB");
	Log.d("", "可用的block数目：:" + availCount + ",可用大小:" + availCount
			 * blockSize / 1024 + "KB");
}
void readSystem() {
	File root = Environment.getRootDirectory();
	StatFs sf = new StatFs(root.getPath());
	long blockSize = sf.getBlockSize();
	long blockCount = sf.getBlockCount();
	long availCount = sf.getAvailableBlocks();
	Log.d("", "block大小:" + blockSize + ",block数目:" + blockCount + ",总大小:
			+ blockSize * blockCount / 1024 + "KB");
	Log.d("", "可用的block数目：:" + availCount + ",可用大小:" + availCount
			 * blockSize / 1024 + "KB");
}
```
StatFs获取的都是以block为单位的，这里我解释一下block的概念：
1.硬件上的 block size, 应该是"sector size"，linux的扇区大小是512byte。
2.有文件系统的分区的block size, 是"block size"，大小不一，可以用工具查看。
3.没有文件系统的分区的block size，也叫“block size”，大小指的是1024 byte。
4.Kernel buffer cache 的block size, 就是"block size"，大部分PC是1024。
5.磁盘分区的"cylinder size"，用fdisk 可以查看。
我们这里的block size是第二种情况，一般SD卡都是fat32的文件系统，block size是4096。
这样就可以知道手机的内部存储空间和sd卡存储空间的总大小和可用大小了。