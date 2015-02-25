Android TabWidget/TabHost有两种使用方法： 
第一种：使用系统自带写好的TabHost（及继承自TabActivity类）具体代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/androidv
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:id="@+id/tab1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        androidrientation="vertical" >
        <TextView
            android:id="@+id/TextView1"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="This is a tab1" >
        </TextView>
    </LinearLayout>
    <LinearLayout
        android:id="@+id/tab2"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        androidrientation="vertical" >
        <TextView
            android:id="@+id/TextView2"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="This is a tab2" >
        </TextView>
    </LinearLayout>
    <LinearLayout
        android:id="@+id/tab3"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        androidrientation="vertical" >
        <TextView
            android:id="@+id/TextView3"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="This is a tab3" >
        </TextView>
    </LinearLayout>
</FrameLayout>
```
代码如下：
```  
import android.app.AlertDialog;
import android.app.Dialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.widget.TabHost;
public class Test_TabWidget extends TabActivity {
	[Tags]/** Called when the activity is first created. */
	private TabHost tabHost;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
		tabHost = this.getTabHost();
		LayoutInflater li = LayoutInflater.from(this);
		li.inflate(R.layout.main, tabHost.getTabContentView(), true);
		tabHost.addTab(tabHost
				.newTabSpec("Tab_1")
				.setContent(R.id.tab1)
				.setIndicator("TAB1",
						this.getResources().getDrawable(R.drawable.img1)));
		tabHost.addTab(tabHost
				.newTabSpec("Tab_2")
				.setContent(R.id.tab2)
				.setIndicator("TAB2",
						this.getResources().getDrawable(R.drawable.img2)));
		tabHost.addTab(tabHost
				.newTabSpec("Tab_3")
				.setContent(R.id.tab3)
				.setIndicator("TAB3",
						this.getResources().getDrawable(R.drawable.img3)));
		tabHost.setCurrentTab(1);
		// tabHost.setBackgroundColor(Color.GRAY);
		tabHost.setOnTabChangedListener(new TabHost.OnTabChangeListener() {
			public void onTabChanged(String tabId) {
				Dialog dialog = new AlertDialog.Builder(Test_TabWidget.this)
						.setTitle("提示")
						.setMessage("选中了" + tabId + "选项卡")
						.setIcon(R.drawable.icon)
						.setPositiveButton("确定",
								new DialogInterface.OnClickListener() {
									public void onClick(DialogInterface dialog,
											int which) {
									}
								}).create();
				dialog.show();
			}
		});
	}
}
```
第二种：就是定义我们自己的tabHost：不用继承TabActivity，具体代码如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/TabHost01"
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
            <LinearLayout
                android:id="@+id/LinearLayout1"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" >
                <TextView
                    android:id="@+id/TextView01"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="one" >
                </TextView>
            </LinearLayout>
            <LinearLayout
                android:id="@+id/LinearLayout2"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" >
                <TextView
                    android:id="@+id/TextView02"
                    android:layout_width="fill_parent"
                    android:layout_height="wrap_content"
                    android:text="two" >
                </TextView>
            </LinearLayout>
            <LinearLayout
                android:id="@+id/LinearLayout3"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" >
                <TextView
                    android:id="@+id/TextView03"
                    android:layout_width="fill_parent"
                    android:layout_height="wrap_content"
                    android:text="three" >
                </TextView>
            </LinearLayout>
        </FrameLayout>
    </LinearLayout>
</TabHost>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.widget.TabHost;
public class Test_TabHost extends Activity {
	private TabHost tabHost;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		try {
			tabHost = (TabHost) this.findViewById(R.id.TabHost01);
			tabHost.setup();
			tabHost.addTab(tabHost
					.newTabSpec("tab_1")
					.setContent(R.id.LinearLayout1)
					.setIndicator("TAB1",
							this.getResources().getDrawable(R.drawable.img1)));
			tabHost.addTab(tabHost
					.newTabSpec("tab_2")
					.setContent(R.id.LinearLayout2)
					.setIndicator("TAB2",
							this.getResources().getDrawable(R.drawable.img2)));
			tabHost.addTab(tabHost
					.newTabSpec("tab_3")
					.setContent(R.id.LinearLayout3)
					.setIndicator("TAB3",
							this.getResources().getDrawable(R.drawable.img3)));
			tabHost.setCurrentTab(1);
		} catch (Exception ex) {
			ex.printStackTrace();
			Log.d("EXCEPTION", ex.getMessage());
		}
	}
}
```
注意：第二种方法时布局文件中的TabWidget的id必须定义为：android:id="@android:id/tabs"，FrameLayout的id必须定义为：android:id="@android:id/tabcontent" 其它控件没有限制，否则报错。