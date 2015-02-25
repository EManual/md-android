我们常常会遇到这样一种情况，比如我的某个工程特别的大，想分成几个子工程来做，或者在某些时候想引用外部的apk为自己所用，想在安装自己程序apk的时候一起安装关联的apk
把你关联的apk放在 assets目录下面。代码如下，只是在安装关联apk的时候是显示安装的。
```  
private File getAssetFile() {
	AssetManager asset = this.getAssets();
	try {
		InputStream is = asset.open("Zxing.apk");
		FileOutputStream fos = this.openFileOutput("Zxing.apk",
				Context.MODE_PRIVATE + Context.MODE_WORLD_READABLE);
		byte[] buffer = new byte[1024];
		int len = 0;
		while ((len = is.read(buffer)) != -1) {
			fos.write(buffer, 0, len);
		}
		fos.flush();
		is.close();
		fos.close();
	} catch (IOException e) {
		e.printStackTrace();
	}
	return new File("Zxing.apk");
}
private void installApk(File file) {
	Intent intent = new Intent();
	intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	intent.setAction(Intent.ACTION_VIEW);
	String type = "android/vnd.android.package-archive";
	intent.setDataAndType(Uri.fromFile(file), type);
	startActivity(intent);
}
```