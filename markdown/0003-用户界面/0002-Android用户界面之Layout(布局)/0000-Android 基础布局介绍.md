#### 用布局（layout）资源
布局资源是Android中最常使用的一种资源，Android可以将屏幕中组件的布局方式定义在一个XML文件中，这有点像Web开发中的HTML页面。我们可以调用Activity.setContentView()方法，将布局文件展示在Activity上。Android通过LayoutInflater类将XML文件中的组件解析为可视化的视图组件。布局文件保存在res\layout\文件夹中，文件名称任意。
#### 布局文件的定义
我们将通过表来说明布局文件的定义情况。
![img](P)  
下面通过一个实例来演示布局文件的用法。该实例定义一个布局文件，在该布局文件中添加一个TextView、一个EditText和一个Button，分别设置其属性，并且使用Activity.setContentView()方法将其设置为Activity的界面，使用findViewById()方法来得到布局中的组件。
在工程的res\layout\目录下创建一个test_layout.xml布局文件，在该布局文件中使用LinearLayout嵌套TableLayout进行布局管理，其中添加TextView、EditText和Button三个视图组件，并为其设置属性。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!-- 以上四个属性分别是命名空间、 组件布局方向(这里是垂直)、布局的宽(充满屏幕)和高(充满屏幕) -->
    <!-- 以下嵌套一个TableLayout -->
    <TableLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:stretchColumns="1" >
        <TableRow>
            <TextView
                android:id="@+id/layoutTextView01"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="测试Layout："
                android:textColor="@color/red_bg" />
            <!-- 以上五个属性分别是：文本内容、引用组件的ID、该组件的宽(内容的宽)、该组件的高(内容的高)、文件颜色 -->
            <EditText
                android:id="@+id/EditText01"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:text="" />
        </TableRow>
        <TableRow android:gravity="right" >
            <Button
                android:id="@+id/layoutButton01"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Test" />
        </TableRow>
    </TableLayout>
</LinearLayout>
```
在工程的com.amaker.ch03.layout包中创建一个TestLayoutActivity类，在该类的顶部声明TextView、EditText和Button。在onCreate()方法中定义setContentView()方法，将布局文件设置为Activity的界面，使用findViewById()方法实例化以上三个视图组件。
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
public class TestLayoutActivity extends Activity {
	private TextView myTextView;
	private EditText myEditText;
	private Button myButton;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 设置Activity的界面布局
		setContentView(R.layout.test_layout);
		// 通过findViewByIdff获得TextView实例
		myTextView = (TextView) findViewById(R.id.layoutText);
		// 通过findViewById方法获得EditText实例
		myEditText = (EditText) findViewById(R.id.layoutEdit);
		// 通过findViewById方法获得Button实例
		myButton = (Button) findViewById(R.id.layoutButton01);
	}
}
```
效果图：
![img](P)  