上次讲了如何使用Gallery控件，这次就讲Gallery 与ImageSwitcher的结合使用，本文实现一个简单的浏览图片的功能。
除了Gallery可以拖拉切换图片，我在ImageSwitcher控件加入了setOnTouchListener事件实现，使得ImageSwitcher也可以在拖拉中切换图片。本例子依然使用JAVA的反射机制来自动读取资源中的图片。
main.xml的源码如下:
```     
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <ImageSwitcher
        android:id="@+id/switcher"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
    <Gallery
        android:id="@+id/gallery"
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:layout_alignParentBottom="true
        android:layout_alignParentLeft="true
        android:background="55000000"
        android:gravity="center_vertical"
        android:spacing="16dp" />
</RelativeLayout>
```
程序的源码如下:
```   
public class testImageView extends Activity implements ViewFactory {
	private ImageSwitcher is;
	private Gallery gallery;
	private int downX, upX;
	private ArrayList<Integer> imgList = new ArrayList<Integer>();// 图像ID
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 用反射机制来获取资源中的图片ID
		Field[] fields = R.drawable.class.getDeclaredFields();
		for (Field field : fields) {
			if (!"icon".equals(field.getName()))// 除了icon之外的图片
			{
				int index = 0;
				try {
					index = field.getInt(R.drawable.class);
				} catch (IllegalArgumentException e) {
					e.printStackTrace();
				} catch (IllegalAccessException e) {
					e.printStackTrace();
				}
				// 保存图片ID
				imgList.add(index);
			}
		}
		// 设置ImageSwitcher控件
		is = (ImageSwitcher) findViewById(R.id.switcher);
		is.setFactory(this);
		is.setInAnimation(AnimationUtils.loadAnimation(this,
				android.R.anim.fade_in));
		is.setOutAnimation(AnimationUtils.loadAnimation(this,
				android.R.anim.fade_out));
		is.setOnTouchListener(new OnTouchListener() {
			/*
			 [Tags]* 在ImageSwitcher控件上滑动可以切换图片
			 [Tags]*/
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				if (event.getAction() == MotionEvent.ACTION_DOWN) {
					downX = (int) event.getX();// 取得按下时的坐标
					return true;
				} else if (event.getAction() == MotionEvent.ACTION_UP) {
					upX = (int) event.getX();// 取得松开时的坐标
					int index = 0;
					if (upX - downX > 100)// 从左拖到右，即看前一张
					{
						// 如果是第一，则去到尾部
						if (gallery.getSelectedItemPosition() == 0)
							index = gallery.getCount() - 1;
						else
							index = gallery.getSelectedItemPosition() - 1;
					} else if (downX - upX > 100)// 从右拖到左，即看后一张
					{
						// 如果是最后，则去到第一
						if (gallery.getSelectedItemPosition() == (gallery
								.getCount() - 1))
							index = 0;
						else
							index = gallery.getSelectedItemPosition() + 1;
					}
					// 改变gallery图片所选，自动触发ImageSwitcher的setOnItemSelectedListener
					gallery.setSelection(index, true);
					return true;
				}
				return false;
			}
		});
		// 设置gallery控件
		gallery = (Gallery) findViewById(R.id.gallery);
		gallery.setAdapter(new ImageAdapter(this));
		gallery.setOnItemSelectedListener(new OnItemSelectedListener() {
			@Override
			public void onItemSelected(AdapterView<?> arg0, View arg1,
					int position, long arg3) {
				is.setImageResource(imgList.get(position));
			}
			@Override
			public void onNothingSelected(AdapterView<?> arg0) {
			}
		});
	}
	// 设置ImgaeSwitcher
	@Override
	public View makeView() {
		ImageView i = new ImageView(this);
		i.setBackgroundColor(0xFF000000);
		i.setScaleType(ImageView.ScaleType.CENTER);// 居中
		i.setLayoutParams(new ImageSwitcher.LayoutParams(// 自适应图片大小
				LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT));
		return i;
	}
	public class ImageAdapter extends BaseAdapter {
		public ImageAdapter(Context c) {
			mContext = c;
		}
		public int getCount() {
			return imgList.size();
		}
		public Object getItem(int position) {
			return position;
		}
		public long getItemId(int position) {
			return position;
		}
		public View getView(int position, View convertView, ViewGroup parent) {
			ImageView i = new ImageView(mContext);
			i.setImageResource(imgList.get(position));
			i.setAdjustViewBounds(true);
			i.setLayoutParams(new Gallery.LayoutParams(
					LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT));
			return i;
		}
		private Context mContext;
	}
}
```