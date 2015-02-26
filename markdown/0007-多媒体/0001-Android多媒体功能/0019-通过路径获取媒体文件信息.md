方法一：解析媒体文件
方法二：读取媒体文件数据库：
创建工具包：com.sky_dreaming.tools.media.provider
编写媒体信息封装类：MediaInfo.java
```  
import java.io.UnsupportedEncodingException;
import android.graphics.Bitmap;
 /**
  * Media info beans
  */
public class MediaInfo {
	 /**
	  * play total time
	  */
	private int playDuration = 0;
	 /**
	  * song name
	  */
	private String mediaName = "";
	 /**
	  * album name
	  */
	private String mediaAlbum = "";
	 /**
	  * artist name
	  */
	private String mediaArtist = "";
	 /**
	  * mYear
	  */
	private String mediaYear = "";
	 /**
	  * fileName
	  */
	private String mFileName = "";
	 /**
	  * mFileType
	  */
	private String mFileType = "";
	 /**
	  * mFileSize
	  */
	private String mFileSize = "";
	 /**
	  * mFilePath
	  */
	private String mFilePath = "";
	public Bitmap getmBitmap() {
		return mBitmap;
	}
	public void setmBitmap(Bitmap mBitmap) {
		this.mBitmap = mBitmap;
	}
	private Bitmap mBitmap = null;
	 /**
	  * getPlayDuration
	  */
	public int getPlayDuration() {
		return playDuration;
	}
	 /**
	  * setPlayDuration
	  * @param playDuration
	  */
	public void setPlayDuration(int playDuration) {
		this.playDuration = playDuration;
	}
	 /**
	  * getMediaName
	  * @param playDuration
	  */
	public String getMediaName() {
		return mediaName;
	}
	 /**
	  * setMediaName
	  * @param playDuration
	  */
	public void setMediaName(String mediaName) {
		try {
			mediaName = new String(mediaName.getBytes("ISO-8859-1"), "GBK");
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		}
		this.mediaName = mediaName;
	}
	 /**
	  * getMediaAlbum
	  * @param playDuration
	  */
	public String getMediaAlbum() {
		return mediaAlbum;
	}
	 /**
	  * setMediaAlbum
	  * @param playDuration
	  */
	public void setMediaAlbum(String mediaAlbum) {
		try {
			mediaAlbum = new String(mediaAlbum.getBytes("ISO-8859-1"), "GBK");
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		}
		this.mediaAlbum = mediaAlbum;
	}
	 /**
	  * getMediaArtist
	  * @param playDuration
	  */
	public String getMediaArtist() {
		return mediaArtist;
	}
	 /**
	  * setMediaArtist
	  * @param playDuration
	  */
	public void setMediaArtist(String mediaArtist) {
		try {
			mediaArtist = new String(mediaArtist.getBytes("ISO-8859-1"), "GBK");
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		}
		this.mediaArtist = mediaArtist;
	}
	 /**
	  * getMediaYear
	  * @param playDuration
	  */
	public String getMediaYear() {
		return mediaYear;
	}
	 /**
	  * setMediaYear
	  * @param playDuration
	  */
	public void setMediaYear(String mediaYear) {
		this.mediaYear = mediaYear;
	}
	 /**
	  * getmFileName
	  * @param playDuration
	  */
	public String getmFileName() {
		return mFileName;
	}
	 /**
	  * setmFileName
	  * @param playDuration
	  */
	public void setmFileName(String mFileName) {
		this.mFileName = mFileName;
	}
	 /**
	  * getmFileType
	  * @param playDuration
	  */
	public String getmFileType() {
		return mFileType;
	}
	 /**
	  * setmFileType
	  * @param playDuration
	  */
	public void setmFileType(String mFileType) {
		this.mFileType = mFileType;
	}
	 /**
	  * getmFileSize
	  * @param playDuration
	  */
	public String getmFileSize() {
		return mFileSize;
	}
	 /**
	  * setmFileSize
	  * @param playDuration
	  */
	public void setmFileSize(String mFileSize) {
		this.mFileSize = mFileSize;
	}
	 /**
	  * getmFilePath
	  * @param playDuration
	  */
	public String getmFilePath() {
		return mFilePath;
	}
	 /**
	  * setmFilePath
	  * @param playDuration
	  */
	public void setmFilePath(String mFilePath) {
		this.mFilePath = mFilePath;
	}
}
```
编写数据提供工具类：MediaInfoProvider
```  
import java.io.File;
import android.content.Context;
import android.database.Cursor;
import android.net.Uri;
import android.provider.MediaStore;
import android.provider.MediaStore.MediaColumns;
import android.util.Log;
import android.widget.Toast;
 /**
  * tools to get media file info
  */
public class MediaInfoProvider {
	 /**
	  * context
	  */
	private Context mContext = null;
	 /**
	  * data path
	  */
	private static final String dataPath = "/mnt";
	 /**
	  * query column
	  */
	private static final String[] mCursorCols = new String[] {
			MediaStore.Audio.Media._ID, MediaStore.Audio.Media.DISPLAY_NAME,
			MediaStore.Audio.Media.TITLE, MediaStore.Audio.Media.DURATION,
			MediaStore.Audio.Media.ARTIST, MediaStore.Audio.Media.ALBUM,
			MediaStore.Audio.Media.YEAR, MediaStore.Audio.Media.MIME_TYPE,
			MediaStore.Audio.Media.SIZE, MediaStore.Audio.Media.DATA };
	 /**
	  * MediaInfoProvider
	  * @param context
	  */
	public MediaInfoProvider(Context context) {
		this.mContext = context;
	}
	 /**
	  * get the media file info by path
	  * @param filePath
	  */
	public MediaInfo getMediaInfo(String filePath) {
		/* check a exit file */
		File file = new File(filePath);
		if (file.exists()) {
			Toast.makeText(mContext, "sorry, the file is not exit!",
					Toast.LENGTH_SHORT);
		}
		/* create the query URI, where, selectionArgs */
		Uri Media_URI = null;
		String where = null;
		String selectionArgs[] = null;
		if (filePath.startsWith("content://media/")) {
			/* content type path */
			Media_URI = Uri.parse(filePath);
			where = null;
			selectionArgs = null;
		} else {
			/* external file path */
			if (filePath.indexOf(dataPath) < 0) {
				filePath = dataPath + filePath;
			}
			Media_URI = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
			where = MediaColumns.DATA + "=?";
			selectionArgs = new String[] { filePath };
		}
		/* query */
		Cursor cursor = mContext.getContentResolver().query(Media_URI,
				mCursorCols, where, selectionArgs, null);
		if (cursor == null || cursor.getCount() == 0) {
			return null;
		} else {
			cursor.moveToFirst();
			MediaInfo info = getInfoFromCursor(cursor);
			printInfo(info);
			return info;
		}
	}
	 /**
	  * get the media info beans from cursor
	  * @param cursor
	  */
	private MediaInfo getInfoFromCursor(Cursor cursor) {
		MediaInfo info = new MediaInfo();
		/* file name */
		if (cursor.getString(1) != null) {
			info.setmFileName(cursor.getString(1));
		}
		/* media name */
		if (cursor.getString(2) != null) {
			info.setMediaName(cursor.getString(2));
		}
		/* play duration */
		if (cursor.getString(3) != null) {
			info.setPlayDuration(cursor.getInt(3));
		}
		/* artist */
		if (cursor.getString(4) != null) {
			info.setMediaArtist(cursor.getString(4));
		}
		/* album */
		if (cursor.getString(5) != null) {
			info.setMediaAlbum(cursor.getString(5));
		}
		/* media year */
		if (cursor.getString(6) != null) {
			info.setMediaYear(cursor.getString(6));
		} else {
			info.setMediaYear("undefine");
		}
		/* media type */
		if (cursor.getString(7) != null) {
			info.setmFileType(cursor.getString(7).trim());
		}
		/* media size */
		if (cursor.getString(8) != null) {
			float temp = cursor.getInt(8) / 1024f / 1024f;
			String sizeStr = (temp + "").substring(0, 4);
			info.setmFileSize(sizeStr + "M");
		} else {
			info.setmFileSize("undefine");
		}
		/* media file path */
		if (cursor.getString(9) != null) {
			info.setmFilePath(cursor.getString(9));
		}
		return info;
	}
	 /**
	  * print media info
	  * @param info
	  */
	private void printInfo(MediaInfo info) {
		Log.i("playDuration", "" + info.getPlayDuration());
		Log.i("mediaName", "" + info.getMediaName());
		Log.i("mediaAlbum", "" + info.getMediaAlbum());
		Log.i("mediaArtist", "" + info.getMediaArtist());
		Log.i("mediaYear", "" + info.getMediaYear());
		Log.i("fileName", "" + info.getmFileName());
		Log.i("fileType", "" + info.getmFileType());
		Log.i("fileSize", "" + info.getmFileSize());
		Log.i("filePath", "" + info.getmFilePath());
	}
}
```
可以避免乱码