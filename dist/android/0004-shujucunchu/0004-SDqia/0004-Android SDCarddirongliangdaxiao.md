android.os.StatFs
一个模拟linux的df命令的一个类,获得SD卡和手机内存的使用情况
```  
java.lang.Object
       android.os.StatFs
```
构造方法:
StatFs (String path)
公用方法: 
```  
方法 : getAvailableBlocks ()
返回 : int
解释 :返回文件系统上剩下的可供程序使用的块
方法 : getBlockCount ()
返回 : int
解释 : 返回文件系统上总共的块
方法 : getBlockSize ()
返回 : int
解释 : 返回文件系统 一个块的大小单位byte
方法 : getFreeBlocks ()
返回 : int
解释 : 返回文件系统上剩余的所有块 包括预留的一般程序无法访问的
方法 : restat (String path)
返回 : void
解释 : 执行一个由该对象所引用的文件系统雷斯塔特.(Google翻译)
```
想计算SDCard大小和使用情况时, 只需要得到SD卡总共拥有的Block数或是剩余没用的Block数,再乘以每个Block的大小就是相应的容量大小了单位byte.
```  
public void SDCardSizeTest() {
	// 取得SDCard当前的状态
	String sDcString = android.os.Environment.getExternalStorageState();
	if (sDcString.equals(android.os.Environment.MEDIA_MOUNTED)) {
		// 取得sdcard文件路径
		File pathFile = android.os.Environment
				.getExternalStorageDirectory();
		android.os.StatFs statfs = new android.os.StatFs(pathFile.getPath());
		// 获取SDCard上BLOCK总数
		long nTotalBlocks = statfs.getBlockCount();
		// 获取SDCard上每个block的SIZE
		long nBlocSize = statfs.getBlockSize();
		// 获取可供程序使用的Block的数量
		long nAvailaBlock = statfs.getAvailableBlocks();
		// 获取剩下的所有Block的数量(包括预留的一般程序无法使用的块)
		long nFreeBlock = statfs.getFreeBlocks();
		// 计算SDCard 总容量大小MB
		long nSDTotalSize = nTotalBlocks * nBlocSize / 1024 / 1024;
		// 计算 SDCard 剩余大小MB
		long nSDFreeSize = nAvailaBlock * nBlocSize / 1024 / 1024;
	}
}
```
