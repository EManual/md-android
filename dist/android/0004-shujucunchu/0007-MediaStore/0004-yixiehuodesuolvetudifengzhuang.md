这个是别人的代码，我自己把那些比较难看懂的都加上了注释：
```  
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.database.Cursor;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.provider.MediaStore;
public final class BitmapUti {
	public static Bitmap decodeBitmap(String path, int displayWidth,
			int displayHeight) {
		BitmapFactory.Options op = new BitmapFactory.Options();
		op.inJustDecodeBounds = true;
		// op.inJustDecodeBounds = true;表示我们只读取Bitmap的宽高等信息，不读取像素。
		Bitmap bmp = BitmapFactory.decodeFile(path, op); // 获取尺寸信息
		// op.outWidth表示的是图像真实的宽度
		// op.inSamplySize 表示的是缩小的比例
		// op.inSamplySize = 4,表示缩小1/4的宽和高，1/16的像素，android认为设置为2是最快的。
		// 获取比例大小
		int wRatio = (int) Math.ceil(op.outWidth / (float) displayWidth);
		int hRatio = (int) Math.ceil(op.outHeight / (float) displayHeight);
		// 如果超出指定大小，则缩小相应的比例
		if (wRatio > 1 && hRatio > 1) {
			if (wRatio > hRatio) {
				// 如果太宽，我们就缩小宽度到需要的大小，注意，高度就会变得更加的小。
				op.inSampleSize = wRatio;
			} else {
				op.inSampleSize = hRatio;
			}
		}
		op.inJustDecodeBounds = false;
		bmp = BitmapFactory.decodeFile(path, op);
		return Bitmap
				.createScaledBitmap(bmp, displayWidth, displayHeight, true);
		// Bitmap.createScaledBitmap(Bitmap src,int detWidth,int
		// detHeight,boolean filter)
		// 从原Bitmap创建一个给定宽高的Bitmap
	}
	 /**
	  *  * 采用复杂计算来决定缩放  *
	  * @param path
	  * @param maxImageSize
	  * @return
	  */
	public static Bitmap decodeBitmap(String path, int maxImageSize) {
		BitmapFactory.Options op = new BitmapFactory.Options();
		op.inJustDecodeBounds = true;
		Bitmap bmp = BitmapFactory.decodeFile(path, op);
		// 获取尺寸信息
		int scale = 1;
		if (op.outWidth > maxImageSize || op.outHeight > maxImageSize) {
			// Math.pow(double a, double b)表示返回第一个参数的第二个参数的次幂
			// Math.log（double a）返回double的自然对数,底数是e
			// Math.round(double e)返回最接近double的值
			scale = (int) Math.pow(
					2,
					(int) Math.round(Math.log(maxImageSize
							/ (double) Math.max(op.outWidth, op.outHeight))
							/ Math.log(0.5)));
		}
		op.inJustDecodeBounds = false;
		op.inSampleSize = scale;
		bmp = BitmapFactory.decodeFile(path, op);
		return bmp;
	}
	public static Cursor queryThumbnails(Activity context) {
		String[] columns = new String[] {
				// Thumbnails表示的缩略图的意思
				MediaStore.Images.Thumbnails.DATA,
				MediaStore.Images.Thumbnails._ID,
				MediaStore.Images.Thumbnails.IMAGE_ID };
		return context.managedQuery(
				MediaStore.Images.Thumbnails.EXTERNAL_CONTENT_URI, columns,
				null, null, MediaStore.Images.Thumbnails.DEFAULT_SORT_ORDER);
	}
	public static Cursor queryThumbnails(Activity context, String selection,
			String[] selectionArgs) {
		String[] columns = new String[] { MediaStore.Images.Thumbnails.DATA,
				MediaStore.Images.Thumbnails._ID,
				MediaStore.Images.Thumbnails.IMAGE_ID };
		// public final Cursor managedQuery (Uri uri, String[] projection,
		// String selection, String[] selectionArgs, String sortOrder)
		// uri The URI of the content provider to query.
		// projection List of columns to return.
		// selection SQL WHERE clause.
		// selectionArgs The arguments to selection, if any ?s are pesent
		// sortOrder SQL ORDER BY clause.
		return context.managedQuery(
				MediaStore.Images.Thumbnails.EXTERNAL_CONTENT_URI, columns,
				selection, selectionArgs,
				MediaStore.Images.Thumbnails.DEFAULT_SORT_ORDER);
	}
	// 通过缩略图的ID来查询
	public static Bitmap queryThumbnailById(Activity context, int thumbId) {
		String selection = MediaStore.Images.Thumbnails._ID + " = ?";
		String[] selectionArgs = new String[] { thumbId + "" };
		Cursor cursor = BitmapUti.queryThumbnails(context, selection,
				selectionArgs);
		if (cursor.moveToFirst()) {
			String path = cursor.getString(cursor
					.getColumnIndexOrThrow(MediaStore.Images.Thumbnails.DATA));
			cursor.close();
			return BitmapUti.decodeBitmap(path, 100, 100);
		} else {
			cursor.close();
			return null;
		}
	}
	public static Bitmap[] queryThumbnailsByIds(Activity context,
			Integer[] thumbIds) {
		Bitmap[] bitmaps = new Bitmap[thumbIds.length];
		for (int i = 0; i < bitmaps.length; i++) {
			bitmaps[i] = BitmapUti.queryThumbnailById(context, thumbIds[i]);
		}
		return bitmaps;
	}
	 /**
	  *  * 获取全部
	  * @param context
	  *  */
	public static List<Bitmap> queryThumbnailList(Activity context) {
		List<Bitmap> bitmaps = new ArrayList<Bitmap>();
		Cursor cursor = BitmapUti.queryThumbnails(context);
		for (int i = 0; i < cursor.getCount(); i++) {
			cursor.moveToPosition(i);
			String path = cursor.getString(cursor
					.getColumnIndexOrThrow(MediaStore.Images.Thumbnails.DATA));
			Bitmap b = BitmapUti.decodeBitmap(path, 100, 100);
			bitmaps.add(b);
		}
		cursor.close();
		return bitmaps;
	}
	public static List<Bitmap> queryThumbnailListByIds(Activity context,
			int[] thumbIds) {
		List<Bitmap> bitmaps = new ArrayList<Bitmap>();
		for (int i = 0; i < thumbIds.length; i++) {
			Bitmap b = BitmapUti.queryThumbnailById(context, thumbIds[i]);
			bitmaps.add(b);
		}
		return bitmaps;
	}
	public static Cursor queryImages(Activity context) {
		String[] columns = new String[] { MediaStore.Images.Media._ID,
				MediaStore.Images.Media.DATA,
				MediaStore.Images.Media.DISPLAY_NAME };
		return context.managedQuery(
				MediaStore.Images.Media.EXTERNAL_CONTENT_URI, columns, null,
				null, MediaStore.Images.Media.DEFAULT_SORT_ORDER);
	}
	public static Cursor queryImages(Activity context, String selection,
			String[] selectionArgs) {
		String[] columns = new String[] { MediaStore.Images.Media._ID,
				MediaStore.Images.Media.DATA,
				MediaStore.Images.Media.DISPLAY_NAME };
		return context.managedQuery(
				MediaStore.Images.Media.EXTERNAL_CONTENT_URI, columns,
				selection, selectionArgs,
				MediaStore.Images.Media.DEFAULT_SORT_ORDER);
	}
	public static Bitmap queryImageById(Activity context, int imageId) {
		String selection = MediaStore.Images.Media._ID + "=?";
		String[] selectionArgs = new String[] { imageId + "" };
		Cursor cursor = BitmapUti
				.queryImages(context, selection, selectionArgs);
		if (cursor.moveToFirst()) {
			String path = cursor.getString(cursor
					.getColumnIndexOrThrow(MediaStore.Images.Media.DATA));
			cursor.close();
			// return BitmapUtils.decodeBitmap(path, 260, 260);
			return BitmapUti.decodeBitmap(path, 220);
			// 看看和上面这种方式的差别,看了，差不多
		} else {
			cursor.close();
			return null;
		}
	}
	 /**
	  *  * 根据缩略图的Id获取对应的大图  *
	  * @param context
	  * @param thumbId
	  * @return
	  *  */
	public static Bitmap queryImageByThumbnailId(Activity context,
			Integer thumbId) {
		String selection = MediaStore.Images.Thumbnails._ID + " = ?";
		String[] selectionArgs = new String[] { thumbId + "" };
		Cursor cursor = BitmapUti.queryThumbnails(context, selection,
				selectionArgs);
		if (cursor.moveToFirst()) {
			int imageId = cursor
					.getInt(cursor
							.getColumnIndexOrThrow(MediaStore.Images.Thumbnails.IMAGE_ID));
			cursor.close();
			return BitmapUti.queryImageById(context, imageId);
		} else {
			cursor.close();
			return null;
		}
	}
}
```