最近由于项目原因，需要使用tabhost，写一下自己的使用方法 
实现TabHost有三种方式：继承自TabActivity，ActivityGroup和自定义的Activity 
实现效果图：
![img](P)  
1.使用TabAcitvity 
TabActivity他自己包含一个Tabhost,我们通过getTabhost(),也不需要调用setContentView()设置layout。如果设置一定要按照android SDK的规定进行设置。SDK规定的是：TabHost，TabWidget，FrameLayout的id必须为@android:id/tabhost，@android:id/tabs，@android:id/tabcontent；
```   
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/tabhost"
    android:layout_width="fill_parent"
    android:layout_height="match_parent"
    android:background="@drawable/inde_bg" >
    <LinearLayout
        android:id="@+id/layout1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TabWidget
            android:id="@android:id/tabs"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" >
        </TabWidget>
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" >
        </FrameLayout>
    </LinearLayout>
</TabHost>
```
否则会报什么需要设置相应的id。 
实现代码如下（需要的图片包括在zip文件中）： 
```  
public class IndexActicity extends TabActivity {
	private int index_tab = 0;
	private TabWidget tabWidget;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		DisplayMetrics dm = new DisplayMetrics();
		getWindowManager().getDefaultDisplay().getMetrics(dm);
		Constant.WIDTH = dm.widthPixels;
		Constant.HEIGHT = dm.heightPixels;
		TabHost t = getTabHost();
		t.setBackgroundDrawable(getResources().getDrawable(R.drawable.inde_bg));
		t.setPadding(0, 0, 0, 0);
		tabWidget = t.getTabWidget();
		LayoutInflater fi = LayoutInflater.from(IndexActicity.this);
		View view = fi.inflate(R.layout.tab_layout, null);
		LinearLayout ll = (LinearLayout) view.findViewById(R.id.tablayout);
		View tab1 = view.findViewById(R.id.tab1);
		View tab2 = view.findViewById(R.id.tab2);
		View tab3 = view.findViewById(R.id.tab3);
		View tab4 = view.findViewById(R.id.tab4);
		ll.removeAllViews();
		t.addTab(t.newTabSpec("1").setIndicator(tab1)
				.setContent(new Intent(IndexActicity.this, TabActivity1.class)));
		t.addTab(t.newTabSpec("2").setIndicator(tab2)
				.setContent(new Intent(IndexActicity.this, TabActivity2.class)));
		t.addTab(t.newTabSpec("3").setIndicator(tab3)
				.setContent(new Intent(IndexActicity.this, TabActivity3.class)));
		t.addTab(t.newTabSpec("4").setIndicator(tab4)
				.setContent(new Intent(IndexActicity.this, TabActivity4.class)));
		tabWidget.setBackgroundColor(getResources().getColor(R.color.alpha_00));
		tabWidget.setBaselineAligned(true);
		tab1.setBackgroundDrawable(getResources().getDrawable(
				R.drawable.menu_bg));
		for (int i = 0; i < tabWidget.getChildCount(); i++) {
			tabWidget.getChildAt(i).getLayoutParams().width = Constant.WIDTH / 4;
			tabWidget.getChildAt(i).getLayoutParams().height = 50;
		}
		t.setOnTabChangedListener(new OnTabChangeListener() {
			@Override
			public void onTabChanged(String tabId) {
				tabChanged(tabId);
			}
		});
	}
	// 捕获tab变化事件
	public void tabChanged(String tabId) {
		if (index_tab != (Integer.valueOf(tabId) - 1)) {
			tabWidget.getChildAt(Integer.valueOf(tabId) - 1)
					.setBackgroundDrawable(
							getResources().getDrawable(R.drawable.menu_bg));
			tabWidget.getChildAt(index_tab).setBackgroundDrawable(null);

			index_tab = Integer.valueOf(tabId) - 1;
		}
	}
}
```
备注：我在继承TabActivity的时候，将TabWidget的android:layout_height设置为"fill_parent"时,tab中不显示任何东西。如图
![img](P)  
使用GroupActivity和自定义 
使用GroupActivity和自己定义的Activity的区别不大，主要区别是：GroupActivity主要为了实现每个Tab可以包含不同的Activity，而自己定义的Activity却不能实现，只能显示一些View。所以如果你需要在不同的tab中显示Activity时，则需要继承GroupActivity，或者TabActivity。 
用这两种方法实现需要调用setContentView()设置layout，layout中包括这个tabhost，tabwidget及Fremelayout。而且一定要对tabhost调用setup方法，否则会出现空指针异常，原因是setup方法初始化tabwidget和framelayout对象。 
这个时候的layout如下： 
```   
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/testhost
    android:layout_width="fill_parent"
    android:layout_height="match_parent"
    android:background="@drawable/inde_bg" >
    <LinearLayout
        android:id="@+id/layout1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TabWidget
            android:id="@android:id/tabs"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" >
        </TabWidget>
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" >
        </FrameLayout>
    </LinearLayout>
</TabHost>
```
类如下 
```  
public class IndexActicity extends ActivityGroup {
	private int index_tab = 0;
	private TabWidget tabWidget;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.index_layout);
		DisplayMetrics dm = new DisplayMetrics();
		getWindowManager().getDefaultDisplay().getMetrics(dm);
		Constant.WIDTH = dm.widthPixels;
		Constant.HEIGHT = dm.heightPixels;
		// TabHost t =getTabHost();
		TabHost t = (TabHost) findViewById(R.id.testhost);
		t.setBackgroundDrawable(getResources().getDrawable(R.drawable.inde_bg));
		t.setup(this.getLocalActivityManager());
		t.setPadding(0, 0, 0, 0);
		tabWidget = t.getTabWidget();
		LayoutInflater fi = LayoutInflater.from(IndexActicity.this);
		View view = fi.inflate(R.layout.tab_layout, null);
		LinearLayout ll = (LinearLayout) view.findViewById(R.id.tablayout);
		View tab1 = view.findViewById(R.id.tab1);
		View tab2 = view.findViewById(R.id.tab2);
		View tab3 = view.findViewById(R.id.tab3);
		View tab4 = view.findViewById(R.id.tab4);
		ll.removeAllViews();
		t.addTab(t.newTabSpec("1").setIndicator(tab1)
				.setContent(new Intent(IndexActicity.this, TabActivity1.class)));
		t.addTab(t.newTabSpec("2").setIndicator(tab2)
				.setContent(new Intent(IndexActicity.this, TabActivity2.class)));
		t.addTab(t.newTabSpec("3").setIndicator(tab3)
				.setContent(new Intent(IndexActicity.this, TabActivity3.class)));
		t.addTab(t.newTabSpec("4").setIndicator(tab4)
				.setContent(new Intent(IndexActicity.this, TabActivity4.class)));
		tabWidget.setBackgroundColor(getResources().getColor(R.color.alpha_00));
		tabWidget.setBaselineAligned(true);
		tab1.setBackgroundDrawable(getResources().getDrawable(
				R.drawable.menu_bg));
		for (int i = 0; i < tabWidget.getChildCount(); i++) {
			tabWidget.getChildAt(i).getLayoutParams().width = Constant.WIDTH / 4;
			tabWidget.getChildAt(i).getLayoutParams().height = 50;
		}
		t.setOnTabChangedListener(new OnTabChangeListener() {
			@Override
			public void onTabChanged(String tabId) {
				tabChanged(tabId);
			}
		});
	}
	// 捕获tab变化事件
	public void tabChanged(String tabId) {
		if (index_tab != (Integer.valueOf(tabId) - 1)) {
			tabWidget.getChildAt(Integer.valueOf(tabId) - 1)
					.setBackgroundDrawable(
							getResources().getDrawable(R.drawable.menu_bg));
			tabWidget.getChildAt(index_tab).setBackgroundDrawable(null);

			index_tab = Integer.valueOf(tabId) - 1;
		}
	}
}
```
细心的人可能看到，这个layout中的tabhost的属性id不是android默认的tabhost了，这个时候，就需要在activity中使用findViewById（R.id.XXXX）；当然你也可以使用android的默认id，在类中直接使用：
```  
TabHost t = (TabHost)findViewById(android.R.id.tabhost);
```
但是如果你修改了tabhost的属性id，而在调用的时候又使用默认的id，则会报找不到android.R.id.tabhost标签。 
如果修改了tabwidget和framelayout的id，系统会报需要相应的标签，这个是在setup方法调用的默认id。因为他只会在当前的layout中找android.R.id.tabs和android.R.id.tabcontent。 
如果想让tab显示在下边，只需要将tabwidget和framelayout调换位置即可。 