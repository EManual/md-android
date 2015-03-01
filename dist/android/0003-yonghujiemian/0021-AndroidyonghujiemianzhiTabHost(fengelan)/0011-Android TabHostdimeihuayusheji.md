用到的布局xml文件内容如下：
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
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1" >
        </FrameLayout>
        <TabWidget
            android:id="@android:id/tabs"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true" />
    </LinearLayout>
</TabHost>
```
Activity 中抒写的代码：
```  
public class Snippet {
	// 记得先定义这几个变量
	private TabHost tabHost;
	private TabWidget tabWidget;
	Field mBottomLeftStrip;
	Field mBottomRightStrip;
	public TabHost makeTab() {
		if (this.tabHost == null) {
			tabHost = (TabHost) findViewById(android.R.id.tabhost);
			tabWidget = (TabWidget) findViewById(android.R.id.tabs);
			tabHost.setup();
			tabHost.bringToFront();
			TabSpec nearby_tab = tabHost.newTabSpec("nearby_tab");
			TabSpec history_tab = tabHost.newTabSpec("history_tab");
			TabSpec about_tab = tabHost.newTabSpec("about_tab");
			nearby_tab.setIndicator("first",
					getResources().getDrawable(R.drawable.first)).setContent(
					new Intent(this, first.class));
			history_tab.setIndicator("second",
					getResources().getDrawable(R.drawable.second)).setContent(
					new Intent(this, second.class));
			about_tab.setIndicator("third",
					getResources().getDrawable(R.drawable.third)).setContent(
					new Intent(this, third.class));
			tabHost.addTab(nearby_tab);
			tabHost.addTab(history_tab);
			tabHost.addTab(about_tab);
			if (Integer.valueOf(Build.VERSION.SDK) <= 7) {
				try {
					mBottomLeftStrip = tabWidget.getClass().getDeclaredField(
							"mBottomLeftStrip");
					mBottomRightStrip = tabWidget.getClass().getDeclaredField(
							"mBottomRightStrip");
					if (!mBottomLeftStrip.isAccessible()) {
						mBottomLeftStrip.setAccessible(true);
					}
					if (!mBottomRightStrip.isAccessible()) {
						mBottomRightStrip.setAccessible(true);
					}
					mBottomLeftStrip.set(tabWidget,
							getResources().getDrawable(R.drawable.linee));
					mBottomRightStrip.set(tabWidget, getResources()
							.getDrawable(R.drawable.linee));
				} catch (Exception e) {
					e.printStackTrace();
				}
			} else {
				try {
					mBottomLeftStrip = tabWidget.getClass().getDeclaredField(
							"mLeftStrip");
					mBottomRightStrip = tabWidget.getClass().getDeclaredField(
							"mRightStrip");
					if (!mBottomLeftStrip.isAccessible()) {
						mBottomLeftStrip.setAccessible(true);
					}
					if (!mBottomRightStrip.isAccessible()) {
						mBottomRightStrip.setAccessible(true);
					}
					mBottomLeftStrip.set(tabWidget,
							getResources().getDrawable(R.drawable.linee));
					mBottomRightStrip.set(tabWidget, getResources()
							.getDrawable(R.drawable.linee));
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
			for (int i = 0; i < tabWidget.getChildCount(); i++) {
				final TextView tv = (TextView) tabWidget.getChildAt(i)
						.findViewById(android.R.id.title);
				tv.setTextColor(Color.WHITE);
				View vvv = tabWidget.getChildAt(i);
				if (tabHost.getCurrentTab() == i) {
					vvv.setBackgroundDrawable(getResources().getDrawable(
							R.drawable.focus));
				} else {
					vvv.setBackgroundDrawable(getResources().getDrawable(
							R.drawable.unfocus));
				}
			}
			tabHost.setOnTabChangedListener(new OnTabChangeListener() {
				@Override
				public void onTabChanged(String tabId) {
					for (int i = 0; i < tabWidget.getChildCount(); i++) {
						View vvv = tabWidget.getChildAt(i);
						if (tabHost.getCurrentTab() == i) {
							vvv.setBackgroundDrawable(getResources()
									.getDrawable(R.drawable.focus));
						} else {
							vvv.setBackgroundDrawable(getResources()
									.getDrawable(R.drawable.unfocus));
						}
					}
				}
			});
		} else {
			return tabHost;
		}
		return null;
	}
}
```
![img](P)  