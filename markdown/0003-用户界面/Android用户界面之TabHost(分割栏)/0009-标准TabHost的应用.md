Java代码 
```  
public class Activity01 extends TabActivity {
	// 声明TabHost对象
	TabHost mTabHost;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 取得TabHost对象
		mTabHost = getTabHost();
		/* 为TabHost添加标签 */
		// 新建一个newTabSpec(newTabSpec)
		// 设置其标签和图标(setIndicator)
		// 设置内容(setContent)
		mTabHost.addTab(mTabHost
				.newTabSpec("tab_test1")
				.setIndicator("TAB 1",
						getResources().getDrawable(R.drawable.img1))
				.setContent(R.id.mLayout1));
		mTabHost.addTab(mTabHost
				.newTabSpec("tab_test2")
				.setIndicator("TAB 2",
						getResources().getDrawable(R.drawable.img2))
				.setContent(R.id.mLayout2));
		mTabHost.addTab(mTabHost
				.newTabSpec("tab_test3")
				.setIndicator("TAB 3",
						getResources().getDrawable(R.drawable.img3))
				.setContent(R.id.mLayout3));
		// 设置TabHost的背景颜色
		mTabHost.setBackgroundColor(Color.argb(150, 22, 70, 150));
		// 设置TabHost的背景图片资源
		// mTabHost.setBackgroundResource(R.drawable.bg0);
		// 设置当前显示哪一个标签
		mTabHost.setCurrentTab(0);
		mTabHost.setBackgroundResource(R.drawable.df);
		// 标签切换事件处理，setOnTabChangedListener
		mTabHost.setOnTabChangedListener(new OnTabChangeListener() {
			@Override
			public void onTabChanged(String tabId) {
				Dialog dialog = new AlertDialog.Builder(Activity01.this)
						.setTitle("提示")
						.setMessage("当前选中：" + tabId + "标签")
						.setPositiveButton("确定",
								new DialogInterface.OnClickListener() {
									public void onClick(DialogInterface dialog,
											int whichButton) {
										dialog.cancel();
									}
								}).create();// 创建按钮

				dialog.show();
			}
		});
	}
}
```
main.xml: 
```  
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/tabhost"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <TabWidget
            android:id="@android:id/tabs"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" />
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" >
            <include
                android:id="@+id/mLayout1"
                layout="@layout/layout1" />
            <include
                android:id="@+id/mLayout2"
                layout="@layout/layout2" />
            <include
                android:id="@+id/mLayout3"
                layout="@layout/layout3" />
        </FrameLayout>
    </LinearLayout>
</TabHost>
```
其中layout1,2,3可以自定义。