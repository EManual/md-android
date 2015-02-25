你可以拷贝这两张图片来做实验用
现在创建一个状态图片列表来制定每个选项卡不同状态的时候所指定的图片
①把图排尿保存在res/drawable/目录下
②在res/drawable/目录下创建一个名为ic_tab_artists.xml文件，并且插入如下信息
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- When selected,use grey -->
    <item android:drawable="@drawable/ic_tab_artists_grey" android:state_selected="true"/>
    <!-- When not selected ,use white -->
    <item android:drawable="@drawable/ic_tab_artists_white"/>
</selector>
```
上面这个文件，当选项卡的状态改变的时候，选项卡就会自动的在两种已经定义的图片之间切换。
4、打开res/layout/main.xml文件并且插入如下信息
```  
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/tabhost"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical"
        android:padding="5dp" >
        <TabWidget
            android:id="@android:id/tabs"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" />
        <FrameLayout
            android:id="@android:id/tabcontent"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:padding="5dp" />
    </LinearLayout>
</TabHost>
```
这个布局文件将显示选项卡兵器提供每个Activity之间的导航。
TabHost要求一个TabWidget和一个FrameLayout布局，为了使TabWidget和FrameLayout的位置处于垂直方向，需要一个LinearLayout,FrameLayout是每个选项卡内容的地方，之所以那里的内容是空的是因为在TahHost中将自动为每个Activity嵌入。
注意，TabWidget和FrameLayout元素的ID标签和tabcontent元素，这些名称必须使用，因为TahHost检索他们的引用，它恰好期望的是这些名字
6、编写HelloTabWidget。继承TabWidget
```  
import android.app.TabActivity;
import android.content.Intent;
import android.content.res.Resources;
import android.os.Bundle;
import android.widget.TabHost;
public class HelloTabWidget extends TabActivity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Resources res = getResources(); // Resource object to get Drawables
		TabHost tabHost = getTabHost(); // The activity TabHost
		TabHost.TabSpec spec; // Resusable TabSpec for each tab
		Intent intent; // Reusable Intent for each tab
		// Create an Intent to launch an Activity for the tab (to be reused)
		intent = new Intent().setClass(this, ArtistsActivity.class);
		// Initialize a TabSpec for each tab and add it to the TabHost
		spec = tabHost
				.newTabSpec("artists")
				.setIndicator("Artists",
						res.getDrawable(R.drawable.ic_tab_drawable))
				.setContent(intent);
		tabHost.addTab(spec);
		// Do the same for the other tabs
		intent = new Intent().setClass(this, AlbumsActivity.class);
		spec = tabHost
				.newTabSpec("albums")
				.setIndicator("Albums",
						res.getDrawable(R.drawable.ic_tab_drawable))
				.setContent(intent);
		tabHost.addTab(spec);
		intent = new Intent().setClass(this, SongsActivity.class);
		spec = tabHost
				.newTabSpec("songs")
				.setIndicator("Songs",
						res.getDrawable(R.drawable.ic_tab_drawable))
				.setContent(intent);
		tabHost.addTab(spec);
		tabHost.setCurrentTab(2);
	}
}
```
![img](P)  