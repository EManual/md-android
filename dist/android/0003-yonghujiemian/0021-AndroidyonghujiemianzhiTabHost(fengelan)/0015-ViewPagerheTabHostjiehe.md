前几天看了网上关于ViewPager实现滑动切换的效果。回来试了一下，发现效果确实不错，但是切换的几个页面只是调用了不同的layout，实际上还是在一个Activity里面，对功能编写就不方便了。所以，我想到了TabHost和ViewPager结合，使用TabHost切换Activity，使用ViewPager切换界面，从而晚上切换效果。废话少说，直接看代码吧。
首先是布局的xml
```  
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/tabhost"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:id="@+id/linearLayout1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TabWidget
            android:id="@android:id/tabs"
            android:layout_width="wrap_content"
            android:layout_height="0dip" >
        </TabWidget>
        <LinearLayout
            android:id="@+id/linearLayout1"
            android:layout_width="fill_parent"
            android:layout_height="40dip"
            android:background="@drawable/title" >
            <TextView
                android:id="@+id/text1"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1.0"
                android:gravity="center"
                android:text="@string/black"
                android:textColor="FFFFFF"
                android:textSize="22.0dip" />
            <TextView
                android:id="@+id/text2"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1.0"
                android:gravity="center"
                android:text="@string/gray"
                android:textColor="FFFFFF"
                android:textSize="22.0dip" />
            <TextView
                android:id="@+id/text3"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1.0"
                android:gravity="center"
                android:text="@string/white"
                android:textColor="FFFFFF"
                android:textSize="22.0dip" />
        </LinearLayout>
        <ImageView
            android:id="@+id/cursor"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:scaleType="matrix"
            android:src="@drawable/a" />
        <android.support.v4.view.ViewPager
            android:id="@+id/vPager"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_weight="1.0"
            android:background="000000"
            android:flipInterval="30"
            android:persistentDrawingCache="animation" />
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:visibility="gone" >
        </FrameLayout>
    </LinearLayout>
</TabHost>
```
然后是java文件
```  
import java.util.ArrayList;
import java.util.List;
import android.app.LocalActivityManager;
import android.app.TabActivity;
import android.content.Context;
import android.content.Intent;
import android.graphics.BitmapFactory;
import android.graphics.Matrix;
import android.os.Bundle;
import android.os.Parcelable;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.ViewPager;
import android.support.v4.view.ViewPager.OnPageChangeListener;
import android.util.DisplayMetrics;
import android.util.Log;
import android.view.View;
import android.view.Window;
import android.view.animation.Animation;
import android.view.animation.TranslateAnimation;
import android.widget.ImageView;
import android.widget.TabHost;
import android.widget.TextView;
public class ConfigTabActivity extends TabActivity {
	// 页卡内容
	private ViewPager mPager;
	// Tab页面列表
	private List<View> listViews;
	// 动画图片
	private ImageView cursor;
	// 页卡头标
	private TextView t1, t2, t3;
	// 动画图片偏移量
	private int offset = 0;
	// 当前页卡编号
	private int currIndex = 0;
	// 动画图片宽度
	private int bmpW;
	private LocalActivityManager manager = null;
	private final static String TAG = "ConfigTabActivity";
	private final Context context = ConfigTabActivity.this;
	private TabHost mTabHost;
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		Log.d(TAG, "---onCreate---");
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.config);
		mTabHost = getTabHost();
		mTabHost.addTab(mTabHost.newTabSpec("A").setIndicator("")
				.setContent(new Intent(this, A.class)));
		mTabHost.addTab(mTabHost.newTabSpec("B").setIndicator("")
				.setContent(new Intent(this, B.class)));
		mTabHost.addTab(mTabHost.newTabSpec("C").setIndicator("")
				.setContent(new Intent(this, C.class)));
		mTabHost.setCurrentTab(0);
		manager = new LocalActivityManager(this, true);
		manager.dispatchCreate(savedInstanceState);
		InitImageView();
		InitTextView();
		InitViewPager();
	}
	 /**
	  * 初始化头标
	  */
	private void InitTextView() {
		t1 = (TextView) findViewById(R.id.text1);
		t2 = (TextView) findViewById(R.id.text2);
		t3 = (TextView) findViewById(R.id.text3);
		t1.setOnClickListener(new MyOnClickListener(0));
		t2.setOnClickListener(new MyOnClickListener(1));
		t3.setOnClickListener(new MyOnClickListener(2));
	}
	 /**
	  * 初始化ViewPager
	  */
	private void InitViewPager() {
		mPager = (ViewPager) findViewById(R.id.vPager);
		listViews = new ArrayList<View>();
		MyPagerAdapter mpAdapter = new MyPagerAdapter(listViews);
		Intent intent = new Intent(context, A.class);
		listViews.add(getView("Black", intent));
		Intent intent2 = new Intent(context, B.class);
		listViews.add(getView("Gray", intent2));
		Intent intent3 = new Intent(context, C.class);
		listViews.add(getView("White", intent3));
		mPager.setAdapter(mpAdapter);
		mPager.setCurrentItem(0);
		mPager.setOnPageChangeListener(new MyOnPageChangeListener());
	}
	 /**
	  * 初始化动画
	  */
	private void InitImageView() {
		cursor = (ImageView) findViewById(R.id.cursor);
		bmpW = BitmapFactory.decodeResource(getResources(), R.drawable.a)
				.getWidth();// 获取图片宽度
		DisplayMetrics dm = new DisplayMetrics();
		getWindowManager().getDefaultDisplay().getMetrics(dm);
		int screenW = dm.widthPixels;// 获取分辨率宽度
		offset = (screenW / 3 - bmpW) / 2;// 计算偏移量
		Matrix matrix = new Matrix();
		matrix.postTranslate(offset, 0);
		cursor.setImageMatrix(matrix);// 设置动画初始位置
	}
	 /**
	  * ViewPager适配器
	  */
	public class MyPagerAdapter extends PagerAdapter {
		public List<View> mListViews;
		public MyPagerAdapter(List<View> mListViews) {
			this.mListViews = mListViews;
		}
		@Override
		public void destroyItem(View arg0, int arg1, Object arg2) {
			((ViewPager) arg0).removeView(mListViews.get(arg1));
		}
		@Override
		public void finishUpdate(View arg0) {
		}

		@Override
		public int getCount() {
			return mListViews.size();
		}
		@Override
		public Object instantiateItem(View arg0, int arg1) {
			((ViewPager) arg0).addView(mListViews.get(arg1), 0);
			return mListViews.get(arg1);
		}
		@Override
		public boolean isViewFromObject(View arg0, Object arg1) {
			return arg0 == (arg1);
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
	}
	 /**
	  * 头标点击监听
	  */
	public class MyOnClickListener implements View.OnClickListener {
		private int index = 0;
		public MyOnClickListener(int i) {
			index = i;
		}
		@Override
		public void onClick(View v) {
			mPager.setCurrentItem(index);
		}
	};
	 /**
	  * 页卡切换监听
	  */
	public class MyOnPageChangeListener implements OnPageChangeListener {
		int one = offset * 2 + bmpW;// 页卡1 -> 页卡2 偏移量
		int two = one * 2;// 页卡1 -> 页卡3 偏移量
		@Override
		public void onPageSelected(int arg0) {
			Animation animation = null;
			Intent intent = new Intent();
			switch (arg0) {
			case 0:
				Log.d(TAG, "---0---");
				mTabHost.setCurrentTab(0);
				if (currIndex == 1) {
					animation = new TranslateAnimation(one, 0, 0, 0);
				} else if (currIndex == 2) {
					animation = new TranslateAnimation(two, 0, 0, 0);
				}
				break;
			case 1:
				Log.d(TAG, "---1---");
				mTabHost.setCurrentTab(1);
				if (currIndex == 0) {
					animation = new TranslateAnimation(offset, one, 0, 0);
				} else if (currIndex == 2) {
					animation = new TranslateAnimation(two, one, 0, 0);
				}
				break;
			case 2:
				Log.d(TAG, "---2---");
				mTabHost.setCurrentTab(2);
				if (currIndex == 0) {
					animation = new TranslateAnimation(offset, two, 0, 0);
				} else if (currIndex == 1) {
					animation = new TranslateAnimation(one, two, 0, 0);
				}
				break;
			}
			currIndex = arg0;
			animation.setFillAfter(true);// True:图片停在动画结束位置
			animation.setDuration(300);
			cursor.startAnimation(animation);
		}
		@Override
		public void onPageScrolled(int arg0, float arg1, int arg2) {
		}
		@Override
		public void onPageScrollStateChanged(int arg0) {
		}
	}
	private View getView(String id, Intent intent) {
		return manager.startActivity(id, intent).getDecorView();
	}
}
```
至于每个单独页面的layout和java，大家就根据自己的需求写吧。