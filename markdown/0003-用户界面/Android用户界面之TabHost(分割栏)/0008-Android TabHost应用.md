在market上下载了一个叫芒果TV的应用，粗略分析了一下整个应用的页面布局，应该不会太复杂，决定也山寨一把，模仿一个。借此正好也学习一下android页面设计。
废话不多说，进入正题，整体布局应该是以TabHost为根布局，每个tab标签中应该是以ListView填充（猜测）。截图如下:
![img](P)  
先模仿TabHost部分。首先，新建一个android工程，参见安装Android开发环境，然后修改res/layout目录下的main.xml文件，布局标签改为TabHost，代码片段如下:
```  
<tabhost xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <textview
        android:id="@+id/view1"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="页1" />
    <textview
        android:id="@+id/view2"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="页2" />
    <textview
        android:id="@+id/view3"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:text="页3" />
</tabhost> 
```
最后修改MovieActivity（创建android工程时填写的Activity name），使其继承TabActivity，代码如下：
```  
public class MovieActivity extends TabActivity {
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
		TabHost tabHost = getTabHost();
		LayoutInflater.from(this).inflate(R.layout.main,
				tabHost.getTabContentView(), true);
		tabHost.addTab(tabHost.newTabSpec("tab1").setIndicator("首页")
				.setContent(R.id.view1));
		tabHost.addTab(tabHost
				.newTabSpec("tab2")
				.setIndicator("直播", getResources().getDrawable(R.drawable.icon))
				.setContent(R.id.view2));
		tabHost.addTab(tabHost.newTabSpec("tab3").setIndicator("点播")
				.setContent(R.id.view3));
	}
}
```
代码中红色部分是为tab标签添加图标。运行工程，效果图：
![img](P)  
哈哈，界面暂时比较粗糙。