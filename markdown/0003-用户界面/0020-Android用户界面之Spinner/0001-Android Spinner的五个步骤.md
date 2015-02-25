大家当看完这一片文章以后，我们就可以自定义spinner了，那大家还等什么我们就来看看吧。在Android的UI开发中，Spinner(下拉列表)总是可以用到的，一个简单的自定义Spinner制作我们只需要记住这重要的五步，一个Spinner就可以应用而生了。
(1)新建一个Android工程，名字为SpinnerTest。修改layout下的main.xml，添加一个Textview和一个Spinner，文件内容如下：
```  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget28"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/TextView_Show"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="你选择的是"
        android:textSize="25sp" >
    </TextView>
    <Spinner
        android:id="@+id/spinner_City"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
    </Spinner>
    <!-- 定义一个下拉菜单 -->
</LinearLayout>
```
(2)修改你的SpinnerTest类，在这里我们就要记住五步来自定义一个Spinner了，完整代码及五步注释如下：
```  
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnTouchListener;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Spinner;
import android.widget.TextView;
public class SpinnerTest1 extends Activity {
	private List<String> list = new ArrayList<String>();
	private TextView myTextView;
	private Spinner mySpinner;
	private ArrayAdapter<String> adapter;
	private Animation myAnimation;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 第一步：添加一个下拉列表项的list，这里添加的项就是下拉列表的菜单项
		list.add("北京");
		list.add("上海");
		list.add("深圳");
		list.add("南京");
		list.add("重庆");
		myTextView = (TextView) findViewById(R.id.TextView_Show);
		mySpinner = (Spinner) findViewById(R.id.spinner_City);
		// 第二步：为下拉列表定义一个适配器，这里就用到里前面定义的list。
		adapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_spinner_item, list);
		// 第三步：为适配器设置下拉列表下拉时的菜单样式。
		adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
		// 第四步：将适配器添加到下拉列表上
		mySpinner.setAdapter(adapter);
		// 第五步：为下拉列表设置各种事件的响应，这个事响应菜单被选中
		mySpinner.setOnItemSelectedListener(new Spinner.OnItemSelectedListener() {
				public void onItemSelected(AdapterView<?> arg0, View arg1,
						int arg2, long arg3) {
					/* 将所选mySpinner 的值带入myTextView 中 */
					myTextView.setText("您选择的是：" + adapter.getItem(arg2));
					/* 将mySpinner 显示 */
					arg0.setVisibility(View.VISIBLE);
				}
				public void onNothingSelected(AdapterView<?> arg0) {
					myTextView.setText("NONE");
					arg0.setVisibility(View.VISIBLE);
				}
			});
		/* 下拉菜单弹出的内容选项触屏事件处理 */
		mySpinner.setOnTouchListener(new Spinner.OnTouchListener() {
			public boolean onTouch(View v, MotionEvent event) {
				/* 将mySpinner 隐藏，不隐藏也可以，看自己爱好 */
				v.setVisibility(View.INVISIBLE);
				return false;
			}
		});
		/* 下拉菜单弹出的内容选项焦点改变事件处理 */
		mySpinner.setOnFocusChangeListener(new Spinner.OnFocusChangeListener() {
			public void onFocusChange(View v, boolean hasFocus) {
				v.setVisibility(View.VISIBLE);
			}
		});
	}
}
```
记住这五步后，一个Spinner就Ok了，其中在为Spinner的适配器设置下拉时的菜单样式时，我们可以自定义自己的样式，如果嫌麻烦，就直接用android.R.layout的，就如下面这样。
```  
adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
```