之前在网上找了很多都没有这方面的资料，大概的效果是当Gallery滑动时不下载图片，当Gallery滑动停止时加载当前页面图片，自己花了一点时间大概的实现了，如果各位有更好的意见欢迎说出来大家一起学习。
先上图看看效果：
这图是在加载图片时显示的默认图片，当图片加载完成则替换。
![img](P)  
图片加载完成效果图
![img](P)  
```  
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import android.app.Activity;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.AsyncTask;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.util.Log;
import android.view.KeyEvent;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.Gallery;
public class MyActivity extends Activity implements OnItemClickListener {
	public static HashMap<String, Bitmap> imagesCache = new HashMap<String, Bitmap>(); // 图片缓存
	private Gallery images_ga;
	public static ImageAdapter imageAdapter;
	private int num = 0;
	List<String> urls = new ArrayList<String>(); // 所有图片地址List
	List<String> url = new ArrayList<String>(); // 需要下载图片的url地址
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.gallery_1);
		Files.mkdir(this);
		init();
	}
	private void init() {
		Bitmap image = BitmapFactory.decodeResource(getResources(),
				R.drawable.default_movie_post);
		imagesCache.put("background_non_load", image); // 设置缓存中默认的图片
		images_ga = (Gallery) findViewById(R.id.gallery);
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/f603918fa0ec08fabf7a641659ee3d6d55fbda0d.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/43a7d933c895d143d011bf9273f082025aaf071f.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/63d0f703918fa0ec2ebf584b269759ee3d6ddb7f.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/5ab5c9ea15ce36d31ed8387f3af33a87e850b1a5.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/8601a18b87d6277f6e46217628381f30e924fc2c.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/b48f8c54acf9964c3a29350e.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/bd3eb13533fa828b48da6aabfd1f4134960a5af9.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/29381f30e924b899da3ce5706e061d950a7bf672.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/bd3eb13533fa828b48da6aabfd1f4134960a5af9.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/4bed2e738bd4b31cd73d63fd87d6277f9e2ff877.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/caef76094b36acaf92b619b87cd98d1001e99c24.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/8435e5dde71190efd6154d95ce1b9d16fcfa608a.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/b3de9c824ba1d4cd6d81190f.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/e0fe9925cc2c683834a80f11.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/0bd162d9f2d3572c65911a988a13632762d0c307.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/ac6eddc451da81cb2ac1708a5266d01609243155.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/1bd5ad6e8416d98080cb4a48.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/3c6d55fbb2fb43169d0508ca20a4462309f7d36c.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/faedab64034f78f0daf3664a79310a55b2191c8a.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/2fdda3cc7cd98d10b05af088213fb80e7aec90f9.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/b8014a90f603738d9536f39bb31bb051f819ec0f.jpg");
		urls.add("http://hiphotos.baidu.com/baidu/pic/item/2fdda3cc7cd98d10b05af088213fb80e7aec90f9.jpg");
		imageAdapter = new ImageAdapter(urls, this);
		images_ga.setAdapter(imageAdapter);
		images_ga.setOnItemClickListener(this);
		images_ga.setOnItemSelectedListener(new OnItemSelectedListener() {
			@Override
			public void onItemSelected(AdapterView<?> arg0, View arg1,
					int arg2, long arg3) {
				num = arg2;
				Log.i("mahua", "ItemSelected==" + arg2);
				GalleryWhetherStop();
			}
			@Override
			public void onNothingSelected(AdapterView<?> arg0) {
			}
		});
	}
	@Override
	public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
		Log.i("GOLF", "第" + arg2 + "个被点击了");
	}
	[Tags]/**
	 [Tags]* 判断Gallery滚动是否停止,如果停止则加载当前页面的图片
	 [Tags]*/
	private void GalleryWhetherStop() {
		Runnable runnable = new Runnable() {
			public void run() {
				try {
					int index = 0;
					index = num;
					Thread.sleep(1000);
					if (index == num) {
						url.add(urls.get(num));
						if (num != 0 && urls.get(num - 1) != null) {
							url.add(urls.get(num - 1));
						}
						if (num != urls.size() - 1 && urls.get(num + 1) != null) {
							url.add(urls.get(num + 1));
						}
						Message m = new Message();
						m.what = 1;
						mHandler.sendMessage(m);
					}
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		};
		new Thread(runnable).start();
	}
	// 加载图片的异步任务
	class LoadImageTask extends AsyncTask<String, Void, Bitmap> {
		@Override
		protected void onCancelled() {
			super.onCancelled();
		}
		@Override
		protected void onPostExecute(Bitmap result) {
			super.onPostExecute(result);
		}
		@Override
		protected Bitmap doInBackground(String... params) {
			Bitmap bitmap = null;
			try {
				String url = params[0];
				boolean isExists = Files.compare(url); // 这里是自己写的工具类，判断本地缓存是否已经下载过图片
				if (isExists == false) {// 如果不存在就去下载图片
					Net net = new Net();
					byte[] data = net.downloadResource(MyActivity.this, url);
					bitmap = BitmapFactory
							.decodeByteArray(data, 0, data.length);
					imagesCache.put(url, bitmap); // 把下载好的图片保存到缓存中
					Files.saveImage(url, data);
				} else {// 如果存在直接读取缓存图片
					byte[] data = Files.readImage(url);
					bitmap = BitmapFactory
							.decodeByteArray(data, 0, data.length);
					imagesCache.put(url, bitmap); // 把下载好的图片保存到缓存中
				}
				Message m = new Message();// 图片加载完成通知重新加载
				m.what = 0;
				mHandler.sendMessage(m);
			} catch (Exception e) {
				e.printStackTrace();
			}
			return bitmap;
		}
	}
	private Handler mHandler = new Handler() {
		public void handleMessage(Message msg) {
			try {
				switch (msg.what) {
				case 0: {
					imageAdapter.notifyDataSetChanged();
					break;
				}
				case 1: {
					for (int i = 0; i < url.size(); i++) {
						LoadImageTask task = new LoadImageTask();// 异步加载图片
						task.execute(url.get(i));
						Log.i("mahua", url.get(i));
					}
					url.clear();
				}
				}
				super.handleMessage(msg);
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	};
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		if (keyCode == KeyEvent.KEYCODE_BACK) {
			System.exit(0);
		}
		return super.onKeyDown(keyCode, event);
	}
}
import java.util.List;
import android.content.Context;
import android.content.res.AssetManager;
import android.content.res.TypedArray;
import android.graphics.Bitmap;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
public class ImageAdapter extends BaseAdapter {
	public static final BaseAdapter Adapter = null;
	private List<String> imageUrls; // 图片地址list
	private Context context;
	int mGalleryItemBackground;
	public ImageAdapter(List<String> imageUrls, Context context) {
		this.imageUrls = imageUrls;
		this.context = context;
		// /*
		// * 使用在res/values/attrs.xml中的<declare-styleable>定义 的Gallery属性.
		// */
		TypedArray a = context.obtainStyledAttributes(R.styleable.Gallery1);
		/* 取得Gallery属性的Index id */
		mGalleryItemBackground = a.getResourceId(
				R.styleable.Gallery1_android_galleryItemBackground, 0);
		/* 让对象的styleable属性能够反复使用 */
		a.recycle();
	}
	public int getCount() {
		return imageUrls.size();
	}
	public Object getItem(int position) {
		return imageUrls.get(position);
	}
	public long getItemId(int position) {
		return position;
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		Bitmap image;
		ImageView view = new ImageView(context);
		image = MyActivity.imagesCache.get(imageUrls.get(position));
		// 从缓存中读取图片
		if (image == null) {
			image = MyActivity.imagesCache.get("background_non_load");
		}
		// 设置所有图片的资源地址
		view.setImageBitmap(image);
		view.setScaleType(ImageView.ScaleType.FIT_XY);
		view.setLayoutParams(new Gallery.LayoutParams(240, 320));
		view.setBackgroundResource(mGalleryItemBackground);
		/* 设置Gallery背景图 */
		return view;
	}
}
```
attrs.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<declare-styleable name="TogglePrefAttrs">
		<attr name="android:preferenceLayoutChild" />
	</declare-styleable>
	<!-- These are the attributes that we want to retrieve from the theme
	in view/Gallery1.java -->
	<declare-styleable name="Gallery1">
		<attr name="android:galleryItemBackground" />
	</declare-styleable>
	<declare-styleable name="LabelView">
		<attr name="text" format="string" />
		<attr name="textColor" format="color" />
		<attr name="textSize" format="dimension" />
	</declare-styleable>
</resources>
```
主要的结构就这样了，下载文件和文件保存自己随便写写就行了。如有更好的建议欢迎提出了，大家一起分享学习。