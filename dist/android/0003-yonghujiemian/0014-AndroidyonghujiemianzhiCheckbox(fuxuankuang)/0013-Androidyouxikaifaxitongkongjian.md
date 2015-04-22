CheckBox是Android系统最普通的UI控件，继承了Button按钮
下面通过一个实例来学习
功能：实现复选框的功能
创建项目“CheckBoxProject”
运行项目效果截图：
![img](http://emanual.github.io/md-android/img/view_checkbox/14_checkbox.jpg) 
代码实现：
main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <CheckBox
        android:id="@+id/cb1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/cb1" />
    <CheckBox
        android:id="@+id/cb2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/cb2" />
    <CheckBox
        android:id="@+id/cb3"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/cb3" />
</LinearLayout>
```
string.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<string name="hello">CheckBoxProject!</string>
	<string name="app_name">CheckBox</string>
	<string name="cb1">CheckBox1</string>
	<string name="cb2">CheckBox2</string>
	<string name="cb3">CheckBox3</string>
</resources>
```
CheckBoxProject.java
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.CompoundButton.OnCheckedChangeListener;
import android.widget.Toast;
public class CheckBoxActivity extends Activity implements
		OnCheckedChangeListener {
	private CheckBox cb1, cb2, cb3;// 创建3个CheckBox对象
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 实例化3个CheckBox
		cb1 = (CheckBox) findViewById(R.id.cb1);
		cb2 = (CheckBox) findViewById(R.id.cb2);
		cb3 = (CheckBox) findViewById(R.id.cb3);
		cb1.setOnCheckedChangeListener(this);
		cb2.setOnCheckedChangeListener(this);
		cb3.setOnCheckedChangeListener(this);
	}
	// 重写监听器的抽象函数
	public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
		// buttonView 选中状态发生改变的那个按钮
		// isChecked 表示按钮新的状态(true/false)
		if (cb1 == buttonView || cb2 == buttonView || cb3 == buttonView) {
			if (isChecked) {
				// 显示一个提示信息
				toastDisplay(buttonView.getText() + "选中");
			} else {
				toastDisplay(buttonView.getText() + "取消选中");
			}
		}
	}
	public void toastDisplay(String str) {
		Toast.makeText(this, str, Toast.LENGTH_SHORT).show();
	}
}
```
对CheckBox进行监听，步骤如下：
步骤1：使用OnCheckChangeListener接口，这里的接口导入的是：
```  
android.widget.CompoundButton.OnCheckChangeListener；
```
步骤2：重写监听器的抽象函数"onCheckedChanged()"
步骤3：将每个CheckBox组件绑定监听器。
通过重写的onCheckedChanged(CompoundButton buttonView,boolean isChecked)函数一个参数来确定哪个CheckBox状态发生改变；根据第二个参数来确定改变的CheckeBox的具体状态值，true为勾选，false为未勾选。
CheckBoxActivity类中还定义了toastDisplay()函数，其实是为了使用Android的一种提示信息的方式：Toast：主要用于提示信息，使用起来很方便；先创建Toast对象，然后调用makeText()方法得到一个Toast实例对象。
makeText(Context context,CharSequence text,int duration)
第一参数是上下文对象；第二个参数显示的文本内容；第三个参数显示提示消息的持续时间；其值有两个常量：LENGTH_SHORT(短暂持续)和LENGTH_LONG(略长持续)。
最后，使用Toast对象调用show()方法即可。