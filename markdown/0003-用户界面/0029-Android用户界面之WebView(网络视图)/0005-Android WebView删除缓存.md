1.删除保存于手机上的缓存.
```  
// clear the cache before time numDays
private int clearCacheFolder(File dir, long numDays) {
	int deletedFiles = 0;
	if (dir != null && dir.isDirectory()) {
		try {
			for (File child : dir.listFiles()) {
				if (child.isDirectory()) {
					deletedFiles += clearCacheFolder(child, numDays);
				}
				if (child.lastModified() < numDays) {
					if (child.delete()) {
						deletedFiles++;
					}
				}
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	return deletedFiles;
}
```
调用：clearCacheFolder(Activity.getCacheDir()， System.currentTimeMillis())；//删除此时之前的缓存.
2. 打开关闭使用缓存：
优先使用缓存：
1.WebView.getSettings().setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK)；
不使用缓存：
1.WebView.getSettings().setCacheMode(WebSettings.LOAD_NO_CACHE)；
3.在退出应用的时候加上如下代码：
```  
File file = CacheManager.getCacheFileBaseDir();
if (file != null && file.exists() && file.isDirectory()) {
	for (File item : file.listFiles()) {
		item.delete();
	}
	file.delete();
}
context.deleteDatabase("webview.db");
context.deleteDatabase("webviewCache.db");
```