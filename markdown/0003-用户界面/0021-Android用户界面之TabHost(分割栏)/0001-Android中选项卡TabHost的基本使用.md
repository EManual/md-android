今天来学习一下选项卡（TabHost）的使用,选项卡的使用很常见，比如说：我们在手机上面已接来电，未接来电的分组，首先来看下实现出来的效果截图:
![img](P)  
我们要去实现TabHost,主要有两种方法:
一
1、各选项内容在布局文件中定义。
2、主Activity类继承TabActivity；
3、用getTabHost()方法获取TabHost
二
1、直接在布局文件中定义TabHost
注意：TabWidget的id必须是@android:id/tabs，FrameLayout的id必须是@android:id/tabcontent。 
接下来使用第一种的实现方法来去实现TabHost
主Activity类:
```  
import android.app.TabActivity;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.widget.TabHost;
import android.widget.TabHost.TabSpec;
public class TabHostActivity_Second extends TabActivity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.tabhost_second);
		// 得到TabHost
		TabHost tabHost = this.getTabHost();
		// 把自己的布局文件添加到TabHost 的FrameLayout中 【注意】很重要的一句代码
		LayoutInflater.from(this).inflate(R.layout.tabhost_second,
				tabHost.getTabContentView(), true);
		// 设置选项卡
		// 参数：是选项卡的标签
		TabSpec parentSpec = tabHost.newTabSpec("parent");
		parentSpec.setIndicator("基类",
				this.getResources().getDrawable(R.drawable.announcements256));
		parentSpec.setContent(R.id.tab_1);
		TabSpec subSpec = tabHost.newTabSpec("sub");
		subSpec.setIndicator("子类",
				this.getResources().getDrawable(R.drawable.content256));
		subSpec.setContent(R.id.tab_2);
		tabHost.addTab(parentSpec);
		tabHost.addTab(subSpec);
	}
}
```
然后使用第二种方法创建TabHost
主Activity类：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.TabHost;
import android.widget.TabHost.TabSpec;
 /**
  * 本例是实现TabHost----->直接在XML文件中进行配置 【注意】在xml文件中
  * TahWidget和FrameLayout标签中的ID,必须要使用Android中默认的
  */
public class TabHostActivity_First extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.tabhost_first);
		// 获取TabHost
		TabHost tabHost = (TabHost) findViewById(R.id.tabs);
		tabHost.setup();
		// 设置选项卡
		// 参数：是选项卡的标签
		TabSpec parentSpec = tabHost.newTabSpec("parent");
		parentSpec.setIndicator("基类",
				this.getResources().getDrawable(R.drawable.announcements256));
		parentSpec.setContent(R.id.tab_1);
		TabSpec subSpec = tabHost.newTabSpec("sub");
		subSpec.setIndicator("子类",
				this.getResources().getDrawable(R.drawable.content256));
		subSpec.setContent(R.id.tab_2);
		tabHost.addTab(parentSpec);
		tabHost.addTab(subSpec);
	}
}
```
布局文件： 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TabHost
        android:id="@+id/tabs"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <LinearLayout
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
                android:layout_width="fill_parent"
                android:layout_height="fill_parent" >
                <TextView
                    android:id="@+id/tab_1"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:text="jiangqq_TabHostDemo_First" >
                </TextView>
                <TextView
                    android:id="@+id/tab_2"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:text="jiangqq_TabHostDemo_Second" >
                </TextView>
            </FrameLayout>
        </LinearLayout>
    </TabHost>
</LinearLayout>
```
大家可以比较一下两种方法的异同点，差异不是很大，创建起来也比较简单