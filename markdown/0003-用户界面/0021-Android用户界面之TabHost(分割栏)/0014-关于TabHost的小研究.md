TabHost的使用估计大家都经历过了，有两种方法使用。
1、通过继承TabActivity使用，这种方法是最简单的。
```  
final TabHost tabHost = getTabHost();
tabHost.addTab(tabHost.newTabSpec("tab1")
		.setIndicator("list")
		.setContent(new Intent(this, List1.class)));
tabHost.addTab(tabHost.newTabSpec("tab2")
		.setIndicator("photo list")
		.setContent(new Intent(this, List8.class)));
```
注意:使用继承的TabActivity 不需要再写 setContentView();函数。   
这样就可以显示出来了，但是无法在TabHost外再加布局，所以也不是很好用，但是能够使用大部分的东西。
2、通过 xml构建自己的TabHost。
见xml:
```  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <Button
        android:id="@+id/button"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="a" >
    </Button>
    <ImageView
        android:layout_width="fill_parent"
        android:layout_height="50px"
        android:background="@drawable/facebook" >
    </ImageView>
    <TabHost
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/tabhost"
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
            </FrameLayout>
        </LinearLayout>
    </TabHost>
</LinearLayout>
```
其中TabHost 里面要加上TabWidget 和 FrameLayout ，而且id不允许改变，要用系统自己给的。
Tab1 xml :
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/LinearLayout01" android:layout_width="wrap_content"
        android:layout_height="wrap_content" >
        <TextView android:text="tab1 with linear layout
                android:id="@+id/TextView01" android:layout_width="wrap_content"
                android:layout_height="wrap_content">
        </TextView>
</LinearLayout>
//Tab2 也是一样的
```
构造TabHost:
```  
setContentView(R.layout.main);
final TabHost mTabHost = (TabHost) findViewById(R.id.tabhost);
mTabHost.setup();
LayoutInflater inflater_tab1 = LayoutInflater.from(this);
inflater_tab1
		.inflate(R.layout.Tab1, mTabHost.getTabContentView(), true);
inflater_tab1.inflate(R.layout.tab2, mTabHost.getTabContentView());
mTabHost.addTab(mTabHost.newTabSpec("tab_test1")
		.setIndicator("Friends").setContent(R.id.LinearLayout01));
mTabHost.addTab(mTabHost.newTabSpec("tab_test2").setIndicator("TAB b")
		.setContent(R.id.LinearLayout02));
setTabColors(mTabHost);
mTabHost.setOnTabChangedListener(new TabHost.OnTabChangeListener() {
	@Override
	public void onTabChanged(String tabId) {
		setTabColors(mTabHost);
	}
});
```
用这个函数来改变TabHost里的颜色。注：字体颜色我没研究怎么改，大家可以试试
```  
private void setTabColors(TabHost tabHost) {
	for (int i = 0; i < tabHost.getTabWidget().getChildCount(); i++) {
		// tabHost.getTabWidget().getChildAt(i).getLayoutParams().height =
		// 40;
		tabHost.getTabWidget().getChildAt(i).setBackgroundColor(Color.BLUE);
	}
	tabHost.getTabWidget().getChildAt(tabHost.getCurrentTab())
			.setBackgroundColor(Color.WHITE);
}```
这种方法有一点不好，（也许是我没研究透），无法直接给tabHost.setContent()传一个Activity,而都是从xml里面导入的，这就导致了无法让里面的内容动态更改，如果对这个方面有哦研究的朋友可以教下我。