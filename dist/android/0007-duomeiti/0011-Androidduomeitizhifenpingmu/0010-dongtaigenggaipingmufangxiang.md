当我们手机方向改变时，屏幕也会跟着改变，在这Android当中是很容易实现的．Demo主要是界面有一个按钮，当点击时，如果屏幕方向是横排(PORTRAIT)刚将屏幕方向更改为竖排(LANDSCAPE)，反之依然！我们这里主要是运用了getRequestedOrientation()和setRequestedorientation()两个方法．但是要利用这两个方法必须先在AndroidManiefst.xml设置一下屏幕方属性，不然程序将不能正常的工作．下面我将一步一步教你如何实现该Demo.
1:我们建立一个Android工程,命名为ChangeOrientationDemo.
2:设计UI,打开main.xml,将其代码修改如下，我们这里只是增加了一个按钮，其他什么都没有动．
```  
<xml version="1.0" encoding="utf-8">
<LinearLayout
	android:layout_width="fill_parent"
	android:layout_height="fill_parent"
	android:orientation="vertical" >
	<LinearLayout
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		android:text="@string/hello" />
	<android:id
		android:layout_width="wrap_content"
		android:layout_height="wrap_content"
		android:text="Press me Change Orientation" />
</LinearLayout>
```
3:设计主程序ChangeOrientationDemo.java
```  
import android.app.Activity;
import android.content.pm.ActivityInfo;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class ChangeOrientationDemo extends Activity {
	private Button bt1;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 获取资源
		bt1 = (Button) findViewById(R.id.bt1);
		// 增加按钮事件
		bt1.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				// 如果是竖排,则改为横排
				if (getRequestedOrientation() == ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE)
				{
					setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
				}
				// 如果是横排,则改为竖排
				else if (getRequestedOrientation() == ActivityInfo.SCREEN_ORIENTATION_PORTRAIT)
				{
					setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
				}
			}
		});
	}
}
```
4:在AndroidManifest.xml文件里设置默认方向，不然程序不能正常工作哦．
```  
<xml version="1.0" encoding="utf-8">
	package="com.android.test"
	android:versionCode="1"
	android:versionName="1.0"
	android:label="@string/app_name"
	android:screenOrientation="portrait"
```