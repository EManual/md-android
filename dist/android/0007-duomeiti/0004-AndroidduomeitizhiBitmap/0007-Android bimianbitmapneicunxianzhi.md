在编写Android程序的时候，我们总是难免会碰到OOM（OUT OF MEMORY）的错误，那么这个错误究竟是怎么来的呢，可以先看一下这篇文章ANDROID BITMAP内存限制OOM,OUT OF MEMORY。
这里，我使用Gallery来举例，在模拟器中，不会出现OOM错误，但是，一旦把程序运行到真机里，图片文件一多，必然会出现OOM,我们通过做一些额外的处理来避免。
1.创建一个图片缓存对象HashMap dataCache，integer对应Adapter中的位置position，我们只用缓存处在显示中的图片，对于之外的位置，如果dataCache中有对应的图片，我们需要进行回收内存。在这个例子中，Adapter对象的getView方法首先判断该位置是否有缓存的bitmap，如果没有，则解码图片（bitmapDecoder.getPhotoItem，BitmapDecoder类见后面）并返回bitmap对象，设置dataCache在该位置上的bitmap缓存以便之后使用；若是该位置存在缓存，则直接取出来使用，避免了再一次调用底层的解码图像需要的内存开销。有时为了提高Gallery的更新速度，我们还可以预存储一些位置上的bitmap，比如存储显示区域位置外向上3个向下3个位置的bitmap，这样上或下滚动 Gallery时可以加快getView的获取。
```  
public View getView(int position, View convertView, ViewGroup parent) {
	if (convertView == null) {
		LayoutInflater inflater = LayoutInflater.from(context);
		convertView = inflater.inflate(R.layout.photo_item, null);
		holder = new ViewHolder();
		holder.photo = (ImageView) convertView
				.findViewById(R.id.photo_item_image);
		holder.photoTitle = (TextView) convertView
				.findViewById(R.id.photo_item_title);
		holder.photoDate = (TextView) convertView
				.findViewById(R.id.photo_item_date);
		convertView.setTag(holder);
	} else {
		holder = (ViewHolder) convertView.getTag();
	}
	cursor.moveToPosition(position);
	Bitmap current = dateCache.get(position);
	if (current != null) {// 如果缓存中已解码该图片，则直接返回缓存中的图片
		holder.photo.setImageBitmap(current);
	} else {
		current = bitmapDecoder.getPhotoItem(cursor.getString(1), 2);
		holder.photo.setImageBitmap(current);
		dateCache.put(position, current);
	}
	holder.photoTitle.setText(cursor.getString(2));
	holder.photoDate.setText(cursor.getString(4));
	return convertView;
}
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Matrix;
public class BitmapDecoder {
	private static final String TAG = "BitmapDecoder";
	private Context context;
	public BitmapDecoder(Context context) {
		this.context = context;
	}
	public Bitmap getPhotoItem(String filepath, int size) {
		BitmapFactory.Options options = new BitmapFactory.Options();
		options.inSampleSize = size;
		Bitmap bitmap = BitmapFactory.decodeFile(filepath, options);
		bitmap = Bitmap.createScaledBitmap(bitmap, 180, 251, true);
		// 预先缩放，避免实时缩放，可以提高更新率
		return bitmap;
	}
}
```
2.由于Gallery控件的特点，总有一个item处于当前选择状态，我们利用此时进行dataCache中额外不用的bitmap的清理，来释放内存。
```  
@Override
public void onItemSelected(AdapterView<?> parent, View view, int position,
		long id) {
	releaseBitmap();
	Log.v(TAG, "select id:" + id);
}
private void releaseBitmap() {
	// 在这，我们分别预存储了第一个和最后一个可见位置之外的3个位置的bitmap
	// 即dataCache中始终只缓存了（M＝6＋Gallery当前可见view的个数）M个bitmap
	int start = mGallery.getFirstVisiblePosition() - 3;
	int end = mGallery.getLastVisiblePosition() + 3;
	Log.v(TAG, "start:" + start);
	Log.v(TAG, "end:" + end);
	// 释放position<start之外的bitmap资源
	Bitmap delBitmap;
	for (int del = 0; del < start; del++) {
		delBitmap = dateCache.get(del);
		if (delBitmap != null) {
			// 如果非空则表示有缓存的bitmap，需要清理
			Log.v(TAG, "release position:" + del);
			// 从缓存中移除该del->bitmap的映射
			dateCache.remove(del);
			delBitmap.recycle();
		}
	}
	freeBitmapFromIndex(end);
}
 /**
  * 从某一位置开始释放bitmap资源
  * @param index
  */
private void freeBitmapFromIndex(int end) {
	// 释放之外的bitmap资源
	Bitmap delBitmap;
	for (int del = end + 1; del < dateCache.size(); del++) {
		delBitmap = dateCache.get(del);
		if (delBitmap != null) {
			dateCache.remove(del);
			delBitmap.recycle();
			Log.v(TAG, "release position:" + del);
		}

	}
}
```