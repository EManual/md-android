今天用ViewPager这个类实现了同样的效果，这样代码更少，但是效果是一样的。ViewPager是实现左右两个屏幕平滑地切换的一个类，它是Google提供的。       
使用ViewPager首先需要引入android-support-v4.jar这个jar包。具体ViewPager的用法，这里不做介绍，自己从网上搜索吧！
下面先看一下效果：
![img](P)  
![img](P)  
效果请自行体验和上一篇比较。下面上代码：
首先是layout下面的main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <RelativeLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" >
        <LinearLayout
            android:id="@+id/viewGroup"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:layout_marginBottom="30dp"
            android:gravity="center_horizontal"
            android:orientation="horizontal" >
        </LinearLayout>
    </RelativeLayout>
</FrameLayout> 
```
接下来为每一个切换界面设置布局item1.xml
```  
<?xml version="1.0" encoding="UTF-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ImageView
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:background="@drawable/guide01" >
    </ImageView>
</LinearLayout>
```
其他的几个界面布局和这个一样 ，就是修改下背景图片，所以不再复述，最后是核心代码：
```  
import java.util.ArrayList;
import android.app.Activity;
import android.os.Bundle;
import android.os.Parcelable;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.ViewPager;
import android.support.v4.view.ViewPager.OnPageChangeListener;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.view.ViewGroup.LayoutParams;
import android.view.Window;
import android.widget.ImageView;
public class MainActivity extends Activity {
	ViewPager viewPager;
	ArrayList<View> list;
	ViewGroup main, group;
	ImageView imageView;
	ImageView[] imageViews;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		LayoutInflater inflater = getLayoutInflater();
		list = new ArrayList<View>();
		list.add(inflater.inflate(R.layout.item1, null));
		list.add(inflater.inflate(R.layout.item2, null));
		list.add(inflater.inflate(R.layout.item3, null));
		list.add(inflater.inflate(R.layout.item4, null));
		list.add(inflater.inflate(R.layout.item5, null));
		imageViews = new ImageView[list.size()];
		ViewGroup main = (ViewGroup) inflater.inflate(R.layout.main, null);
		// group是R.layou.main中的负责包裹小圆点的LinearLayout.
		ViewGroup group = (ViewGroup) main.findViewById(R.id.viewGroup);
		viewPager = (ViewPager) main.findViewById(R.id.viewPager);
		for (int i = 0; i < list.size(); i++) {
			imageView = new ImageView(MainActivity.this);
			imageView.setLayoutParams(new LayoutParams(10, 10));
			imageView.setPadding(10, 0, 10, 0);
			imageViews[i] = imageView;
			if (i == 0) {
				// 默认进入程序后第一张图片被选中;
				imageViews[i].setBackgroundResource(R.drawable.guide_dot_white);
			} else {
				imageViews[i].setBackgroundResource(R.drawable.guide_dot_black);
			}
			group.addView(imageView);
		}
		setContentView(main);
		viewPager.setAdapter(new MyAdapter());
		viewPager.setOnPageChangeListener(new MyListener());
	}
	class MyAdapter extends PagerAdapter {
		@Override
		public int getCount() {
			return list.size();
		}
		@Override
		public boolean isViewFromObject(View arg0, Object arg1) {
			return arg0 == arg1;
		}
		@Override
		public int getItemPosition(Object object) {
			return super.getItemPosition(object);
		}
		@Override
		public void destroyItem(View arg0, int arg1, Object arg2) {
			((ViewPager) arg0).removeView(list.get(arg1));
		}
		@Override
		public Object instantiateItem(View arg0, int arg1) {
			((ViewPager) arg0).addView(list.get(arg1));
			return list.get(arg1);
		}
		@Override
		public void restoreState(Parcelable arg0, ClassLoader arg1) {
		}
		@Override
		public Parcelable saveState() {
			return null;
		}
		@Override
		public void startUpdate(View arg0) {
		}
		@Override
		public void finishUpdate(View arg0) {
		}
	}
	class MyListener implements OnPageChangeListener {
		@Override
		public void onPageScrollStateChanged(int arg0) {
		}
		@Override
		public void onPageScrolled(int arg0, float arg1, int arg2) {
		}
		@Override
		public void onPageSelected(int arg0) {
			for (int i = 0; i < imageViews.length; i++) {
				imageViews[arg0].setBackgroundResource(R.drawable.guide_dot_white);
				if (arg0 != i) {
					imageViews[i].setBackgroundResource(R.drawable.guide_dot_black);
				}
			}
		}
	}
}
```
最后在提醒一句，不要忘记加入android-support-v4.jar这个jar包。