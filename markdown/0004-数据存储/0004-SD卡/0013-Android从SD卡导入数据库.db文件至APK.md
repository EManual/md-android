代码如下：
```  
private class ImportDatabaseTask extends AsyncTask<Void, Void, String> {
	private final ProgressDialog dialog = new ProgressDialog(mContext);
	@Override
	protected void onPreExecute() {
		this.dialog.setMessage("正在从SD卡导入数据库文件...");
		this.dialog.show();
	}
	@Override
	protected String doInBackground(final Void... args) {
		if (!Environment.MEDIA_MOUNTED.equals(Environment
				.getExternalStorageState())) {
			return "未找到SD卡";
		}
		// goodboyenglish_setting.db　为SD卡根目录上的一个数据库文件
		File dbBackupFile = new File(Environment.getExternalStorageDirectory(),
				"goodboyenglish_setting.db");
		if (!dbBackupFile.exists()) {
			return "找不到SD卡上的数据库文件:goodboyenglish_setting.db";
		} else if (!dbBackupFile.canRead()) {
			return "已找到SD卡上的数据库文件，但无法读取!";
		}
		// setting.db为APK里的一个数据库文件
		File dbFile = new File(Environment.getDataDirectory()
				+ "/data/com.goodboyenglish.leo/databases/setting.db");
		if (dbFile.exists()) {
			dbFile.delete();
		}
		try {
			dbFile.createNewFile();
			// 把dbBackupFile文件里的内容写入dbFile文件(从头写到尾)
			copyFile(dbBackupFile, dbFile);
			shouldRestart = true;
			return "成功的导入数据库文件!";
		} catch (IOException e) {
			return "导入失败!";
		}
	}
	@Override
	protected void onPostExecute(final String msg) {
		if (this.dialog.isShowing()) {
			this.dialog.dismiss();
		}
		Toast.makeText(mContext, msg, Toast.LENGTH_SHORT).show();
	}
	// 拷贝文件的函数 src 为需要复制的文件，dst为目标文件(被src覆盖的文件)
	// 拷贝的过程其实就是把src文件里内容写入dst文件里的过程(从头写到尾)
	public static void copyFile(File src, File dst) throws IOException {
		FileChannel inChannel = new FileInputStream(src).getChannel();
		FileChannel outChannel = new FileOutputStream(dst).getChannel();
		try {
			inChannel.transferTo(0, inChannel.size(), outChannel);
		} finally {
			if (inChannel != null)
				inChannel.close();
			if (outChannel != null)
				outChannel.close();
		}
	}
}
```
用法:new ImportDatabaseTask().execute();