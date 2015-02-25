本文主要内容是如何让Gallery和ImageSwitcher控件能够同步自动播放图片集，看起来较难，然而，实现的方法非常简单，请跟我慢慢来。
如果对Gallery和ImageSwitcher控件不是很熟悉的同学，建议先过去看看，本文并没有怎么讲述控件的使用方法，而是在使用基础上，搭建我们的技巧。
接下来，温习巩固这两个控件的知识点，有个知识性的储备。
#### 一、Gallery的监听事件
Gallery的两个重要监听事件如下：   
1、OnItemClickListener 监听事件
说明：当Gallery中的Item处于选中状态并且被点击触发该事件  ；
其监听方法为：
```  
public void onItemClick(AdapterView<?> parent, View view, int position, long id)
```
2、OnItemSelectedListener  监听事件
说明：当Gallery中的Item处于选中状态时触发该事件
其监听方法为：
```  
public void onItemSelected(AdapterView<?> parent, View view, int position, long id)
```
说明：当Gallery中的Item处于选中状态时触发该事件
```  
public void onNothingSelected(AdapterView<?> parent)
```
说明：当控件没有任何一项item选中时，触发该方法
两种监听事件的区别在于,Item被选中(selected)的由来。其由来有两种：
1、鼠标点击(click)了Item (先click)，然后该项selected ；
2、代码设置某项Item 选中，例如setSelection(int position)(具体使用见下文)，然后该项selected。
在情形1时，首先触发OnItemClickListener(先click)，接着便是OnItemSelectedListener监听(因为item selected)。当某个Item处于选中状态时，如果它是由情形2而来，就不会触发OnItemClickListener监听(没有click)，只会触发OnItemSelectedListener监听
(只是selected)。
#### 二、Gallery的setSelection()方法
方法原型： 
```  
public void setSelection (int position)
```
说明：使第position处于选中状态。同时触发OnItemSelectedListener监听事件。
PS:在listView控件中也存在setSelection()是让该行处于屏幕可见状态，不需要手动滑动定位。第一次进入界面时，默认显示第一行，于是乎，我们可以手动设置该方法，ListView在显示时，便可主动定位该item了。
准备材料已经上齐，相信通过前面的介绍，您一定对Gallery和ImageSwitcher控件很熟悉了，下面准备大餐！
步骤一：开启一个线程，循环遍历图片集的资源id，并且将id发送至Hanlder对象。
步骤二：Handler接受到当前图片资源的ID，调用setSelection (id)选中它(该Item selected)，继而setSelection()
触发OnItemSelectedListener 事件，执行目标方法，这样我们的目的就达到了。
下面，给出该Demo，希望能帮助大家更好的理解。
1、布局文件 main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <ImageSwitcher
        android:id="@+id/myimgSwitcher"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginBottom="20dip" >
    </ImageSwitcher>
    <Gallery
        android:id="@+id/mygallery"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="8A2BE2" >
    </Gallery>
</LinearLayout>
```
2、主文件 MainActivity.java
```  
import java.lang.reflect.Field;
import java.util.ArrayList;
import android.app.Activity;
import android.content.Context;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.util.Log;
import android.view.View;
import android.view.ViewGroup;
import android.view.View.OnClickListener;
import android.widget.AdapterView;
import android.widget.BaseAdapter;
import android.widget.Gallery;
import android.widget.ImageSwitcher;
import android.widget.ImageView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.ViewSwitcher.ViewFactory;
	private static String TAG = "Gallery_Auto";
	private static int MSG_UPDATE = 1;
	private int count_drawble = 0;
	private int cur_index = 0;
	private boolean isalive = true; // 线程循环运行的条件
	private ImageSwitcher imgSwitcher;
	private Gallery mgallery;
	// 为Gallery控件设置Adapter
	private ImageAdapter imgAdapter = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		imgSwitcher = (ImageSwitcher) findViewById(R.id.myimgSwitcher);
		mgallery = (Gallery) findViewById(R.id.mygallery);
		mgallery.setSpacing(3); // 设置图片之间的间隔，如果不加设置 ，图片会叠加。设置为0，表示图片之间无间缝。
		// 设置监听事件 --->当Gallery中的Item处于选中并且被点击触发该事件
		mgallery.setOnItemClickListener(new OnItemClickListener() {
			@Override
			public void onItemClick(AdapterView<?> arg0, View view,
					int position, long arg3) {
				System.out.println("setOnItemClickListener");
				// imgSwitcher.setBackgroundResource(imgAdapter.getResId(position));
			}
		});
		// 当Gallery中的Item处于选中并且被点击触发该事件 ，在该监听事件中，保证图片播放的同步性
		mgallery.setOnItemSelectedListener(new OnItemSelectedListener() {
			@Override
			public void onItemSelected(AdapterView<?> arg0, View view,
					int position, long arg3) {
				System.out.println("setOnItemSelectedListener");
				// 这儿不能通过setImageResource()设置图片
				imgSwitcher.setBackgroundResource(imgAdapter.getResId(position));
			}
			@Override
			public void onNothingSelected(AdapterView<?> arg0) {

			}
		});
		// 构建适配器，并且分配
		imgAdapter = new ImageAdapter(this);
		mgallery.setAdapter(imgAdapter);
		count_drawble = imgAdapter.getCount();
		// 利用线程来更新 当前欲显示的图片id， 调用handler来选中当前图片
		new Thread(new Runnable() {
			@Override
			public void run() {
				while (isalive) {
					cur_index = cur_index % count_drawble; // 图片区间[0,count_drawable)
					Log.i(TAG, "cur_index" + cur_index + " count_drawble --
							+ count_drawble);
					// msg.arg1 = cur_index
					Message msg = mhandler.obtainMessage(MSG_UPDATE, cur_index,
							0);
					mhandler.sendMessage(msg);
					// 更新时间间隔为 2s
					try {
						Thread.sleep(2000);
					} catch (InterruptedException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
					cur_index++; // 放置在Thread.sleep(2000)
									// ；防止mhandler处理消息的同步性，导致cur_index>=count_drawble
				}
			}
		}).start();
	}
	// 通过handler来更新主界面
	// mgallery.setSelection(positon),选中第position的图片，然后调用OnItemSelectedListener监听改变图像
	private Handler mhandler = new Handler() {
		public void handleMessage(Message msg) {
			if (msg.what == MSG_UPDATE) {
				Log.i(TAG, "cur_index" + cur_index);
				mgallery.setSelection(msg.arg1);
				// UI Thread直接更改图片 ，不利用Gallery.OnItemSelectedListener监听更改
				// imgSwitcher.setBackgroundResource(imgAdapter.getResId(msg.arg1));
			}
		}
	};
	public void onDestroy() {
		super.onDestroy();
		isalive = false;
	}
	@Override
	public View makeView() { // ImageSwitcher的ViewFactory方法
		ImageView img = new ImageView(this);
		return img;
	}
	// 为Gallery控件提供适配器的类
	class ImageAdapter extends BaseAdapter {
		private Context mcontext;
		private ArrayList<Integer> residList = new ArrayList<Integer>(); // 通过放射机制保存所有图片的id

		public ImageAdapter(Context context) {
			mcontext = context;
			// 反射的可重用性更好
			// R.id在R文件中本质上是一个类，我们通过这个R.id.class.getClass().getDeclaredFields()可以找到它的所有属性
			Field[] residFields = R.drawable.class.getDeclaredFields();
			for (Field residField : residFields) {
				// 例如： public static final int icon=0x7f020000;
				// 它的Field表示为 : name= icon ; field.getInt() = 0x7f020000
				if (!"icon".equals(residField.getName())) {
					int resid;
					try {
						resid = residField.getInt(null);// 找到该属性的值
						residList.add(resid);
					} catch (IllegalArgumentException e) {
						e.printStackTrace();
					} catch (IllegalAccessException e) {
						e.printStackTrace();
					}
				}
			}
		}
		@Override
		public int getCount() {
			Log.e(TAG, " " + residList.size());
			return residList.size();
		}
		@Override
		public Object getItem(int position) {
			return residList.get(position);
		}
		@Override
		public long getItemId(int position) {
			return 0;
		}
		// 得到该图片的res id
		public int getResId(int position) {
			return residList.get(position);
		}
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			ImageView img;
			if (convertView == null) {
				img = new ImageView(mcontext);
				img.setScaleType(ImageView.ScaleType.FIT_XY);
				img.setLayoutParams(new Gallery.LayoutParams(100, 100)); // 图片显示宽和长
				img.setImageResource(residList.get(position));
			} else {
				img = (ImageView) convertView;
			}
			return img;
		}
	}
}
```
主要的工程逻辑如上 ，由于采用了放射机制，直接添加图片资源便可成功运行了。