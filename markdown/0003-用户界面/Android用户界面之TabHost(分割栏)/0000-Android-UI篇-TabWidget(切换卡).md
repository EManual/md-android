Tab选项卡类似与电话本的界面，通过多个标签切换不同的内容，要实现这个效果，首先要知道TabHost，它是一个用来存放多个Tab标签的容器，每一个Tab都可以对应自己的布局，比如，电话本中的Tab布局就是一个线性布局。
要使用TabHost，首先要通过getTabHost方法获取TabHost的对象，然后通过addTab方法来向TabHost中添加Tab，当然每个Tab在切换时都会产生一个事件，要捕捉这个事件，需要设置TabActivity的事件监听setOnTabChangedListener。
下面是个小例子：
TabTest.java
```  
import android.app.Activity;
import android.app.TabActivity;
import android.graphics.Color;
import android.os.Bundle;
import android.widget.TabHost;
import android.widget.Toast;
import android.widget.TabHost.OnTabChangeListener;
public class TabTest extends TabActivity {
	TabHost tabhost;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 取得TabHost对象
		tabhost = getTabHost();
		// 为TabHost添加标签
		// 新建一个newTabSpec(newTabSpec)
		// 设置其标签和图标（setIndicator）
		// 设置内容（setContent）
		tabhost.addTab(tabhost
				.newTabSpec("tab1")
				.setIndicator("TAB 1",
						getResources().getDrawable(R.drawable.img1))
				.setContent(R.id.text1));
		tabhost.addTab(tabhost
				.newTabSpec("tab2")
				.setIndicator("TAB 2",
						getResources().getDrawable(R.drawable.img2))
				.setContent(R.id.text2));
		tabhost.addTab(tabhost
				.newTabSpec("tab3")
				.setIndicator("TAB 3",
						getResources().getDrawable(R.drawable.img3))
				.setContent(R.id.text3));
		// 设置TabHost的背景颜色
		// tabhost.setBackgroundColor(Color.argb(150,22,70,150));
		// 设置TabHost的背景图片资源
		tabhost.setBackgroundResource(R.drawable.bg0);
		// 设置当前显示哪个标签
		tabhost.setCurrentTab(0);
		// 标签切换事件处理，setOnTabChangedListener
		tabhost.setOnTabChangedListener(new OnTabChangeListener() {
			public void onTabChanged(String tabId) {
				Toast toast = Toast.makeText(getApplicationContext(), "现在是
						+ tabId + "标签", Toast.LENGTH_SHORT);
				toast.show();
			}
		});
	}
}
```
main.xml
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
            <TextView
                android:id="@+id/text1"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:text="选项卡1" />
            <TextView
                android:id="@+id/text2"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:text="选项卡2" />
            <TextView
                android:id="@+id/text3"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:text="选项卡3" />
        </FrameLayout>
    </LinearLayout>
</TabHost>
```
运行效果如下：
![img](P)  