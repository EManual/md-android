三步走:
第一步初始化gallery时设置较大的初始化位置
```  
Gallery gallery = ((Gallery) findViewById(R.id.myGallery1));
gallery.setAdapter(new ImageAdapter(this));
gallery.setSelection(200);
```
第二步:重写 BaseAdapter方法中的getCount时返回一个较大的值:
```  
// 为了使资源循环使用
public int getCount()
{
	return Integer.MAX_VALUE;
}
```
第三步：重写BaseAdapter时使用用position对集合大小取余的值,如下:
```  
/* 取得目前欲显示的图像View，传入数组ID值使之读取与成像 */
public View getView(int position, View convertView, ViewGroup parent) {
	/* 创建一个ImageView对象 */
	ImageView i = new ImageView(this.myContext);
	i.setPadding(10, 10, 10, 10);
	i.setAlpha(80);
	// i.setImageResource(this.myImageIds[position]);
	if (position < 0) {
		position = position + myImageIds.length;
	}
	i.setImageResource(this.myImageIds[position % myImageIds.length]);
	i.setScaleType(ImageView.ScaleType.FIT_XY);
	i.setBackgroundResource(mGalleryItemBackground);
	/* 设置这个ImageView对象的宽高，单位为dip */
	i.setLayoutParams(new Gallery.LayoutParams(85, 72));
	return i;
}
```
以下是该类的完整代码:
/* 依据距离中央的位移量 利用getScale返回views的大小(0.0f to 1.0f) */ 
```  
import android.app.Activity;
import android.content.Context;
import android.content.res.TypedArray;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageView;
import android.widget.TextView;
import android.widget.Toast;
import android.widget.AdapterView.OnItemSelectedListener;
public class EX03_15 extends Activity {
	private TextView mTextView01;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Gallery gallery = ((Gallery) findViewById(R.id.myGallery1));
		gallery.setAdapter(new ImageAdapter(this));
		gallery.setSelection(200);
		gallery.setOnItemSelectedListener(new OnItemSelectedListener() {
			public void onItemSelected(AdapterView<?> arg0, View arg1,
					int arg2, long arg3) {
				Toast.makeText(EX03_15.this, "当前位置:" + arg2, Toast.LENGTH_SHORT).show();
			}
			public void onNothingSelected(AdapterView<?> arg0) {
			}
		});
	}
	public class ImageAdapter extends BaseAdapter {
		/* 类成员 myContext为Context父类 */
		private Context myContext;
		/* 声明GalleryItemBackground */
		int mGalleryItemBackground;
		/* 使用android.R.drawable里的图片作为图库来源，类型为整数数组 */
		private int[] myImageIds = { R.drawable.a1, R.drawable.a2,
				R.drawable.a3, R.drawable.a4, R.drawable.a5, R.drawable.a27 };
		/* 构造器只有一个参数，即要存储的Context */
		public ImageAdapter(Context c) {
			myContext = c;
			/*
			 [Tags]* 使用在res/values/attrs.xml中的<declare-styleable>定义 的Gallery属性.
			 [Tags]*/
			TypedArray a = obtainStyledAttributes(R.styleable.Gallery);
			/* 取得Gallery属性的Index id */
			mGalleryItemBackground = a.getResourceId(
					R.styleable.Gallery_android_galleryItemBackground, 0);
			/* 让对象的styleable属性能够反复使用 */
			a.recycle();
		}
		/* 返回所有已定义的图片总数量 */
		// public int getCount() { return this.myImageIds.length; }
		// 为了使资源循环使用
		public int getCount() {
			return Integer.MAX_VALUE;
		}
		/* 利用getItem方法，取得目前容器中图像的数组ID */
		public Object getItem(int position) {
			return position;
		}
		public long getItemId(int position) {
			return position;
		}
		/* 取得目前欲显示的图像View，传入数组ID值使之读取与成像 */
		public View getView(int position, View convertView, ViewGroup parent) {
			/* 创建一个ImageView对象 */
			ImageView i = new ImageView(this.myContext);
			i.setPadding(10, 10, 10, 10);
			i.setAlpha(80);
			// i.setImageResource(this.myImageIds[position]);
			if (position < 0) {
				position = position + myImageIds.length;
			}
			i.setImageResource(this.myImageIds[position % myImageIds.length]);
			i.setScaleType(ImageView.ScaleType.FIT_XY);
			i.setBackgroundResource(mGalleryItemBackground);
			/* 设置这个ImageView对象的宽高，单位为dip */
			i.setLayoutParams(new Gallery.LayoutParams(85, 72));
			return i;
		}
		/* 依据距离中央的位移量 利用getScale返回views的大小(0.0f to 1.0f) */
		public float getScale(boolean focused, int offset) {
			return Math.max(0, 1.0f / (float) Math.pow(2, Math.abs(offset)));
		}
	}
}
```