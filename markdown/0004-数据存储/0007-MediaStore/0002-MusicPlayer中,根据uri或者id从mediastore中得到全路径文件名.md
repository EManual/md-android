以下代码经过本人亲自验证
```  
// get file's absolute path via id
private String getAbsPath(long id) {
	// Log.d("vrix",Long.toString(id));
	Cursor c = MusicUtils.query(this,
			MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, new String[] {
					MediaStore.Audio.Media._ID,
					MediaStore.Audio.Media.DATA,
					MediaStore.Audio.Media.DISPLAY_NAME },
			MediaStore.Audio.Media._ID + "=" + Long.toString(id), null,
			MediaStore.Audio.Media._ID);
	if (c != null) {
		// Log.d("vrix","count = "+c.getCount()+";column="+c.getColumnCount());
		c.moveToFirst();
		int cid = c.getColumnIndex(MediaStore.Audio.Media.DATA);
		// Log.d("vrix", "getAbsPathFromId(" +
		// c.getString(cid)+")-"+c.getString(c.getColumnIndex(MediaStore.Audio.Media.DISPLAY_NAME)));
		return new String(c.getString(cid));
	}
	// Log.d("vrix","get nothing!");
	return "";
}
// get file's absolute path via uri
private String getAbsPath(Uri uri) {
	// Log.d("vrix","getAbsPath uri = "+uri.toString());
	Cursor c = managedQuery(uri,
			new String[] { MediaStore.Audio.Playlists._ID,
					MediaStore.Audio.Playlists.DATA }, null, null,
			MediaStore.Audio.Playlists._ID);
	if (c != null) {
		c.moveToFirst();
		int cid = c.getColumnIndex(MediaStore.Audio.Playlists.DATA);
		// Log.d("vrix","getAbsPathFromUri("+cid+")="+c.getString(cid));
		return c.getString(cid);
	}
	// Log.d("vrix","get nothing!");
	return "";
}
```