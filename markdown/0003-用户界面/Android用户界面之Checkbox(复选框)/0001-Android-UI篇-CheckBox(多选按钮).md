多选按钮CheckBox的可以实现多项选择，我们可以现在布局文件中定义多选按钮，然后对每一个多选按钮进行事件监听setOnCheckedChangeListener，通过isChecked来判断选项是否被选中。
下面是一个例子，可以很好的理解CheckBox的使用
CheckBoxTest.java
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.Gravity;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.TextView;
import android.widget.Toast;
public class CheckBoxTest extends Activity {
	TextView textview;
	Button submit;
	CheckBox checkbox1;
	CheckBox checkbox2;
	CheckBox checkbox3;
	CheckBox checkbox4;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		textview = (TextView) findViewById(R.id.textview);
		submit = (Button) findViewById(R.id.submit);
		// 取得每一个CheckBox对象
		checkbox1 = (CheckBox) findViewById(R.id.checkbox1);
		checkbox2 = (CheckBox) findViewById(R.id.checkbox2);
		checkbox3 = (CheckBox) findViewById(R.id.checkbox3);
		checkbox4 = (CheckBox) findViewById(R.id.checkbox4);
		// 为每一个选项设置监听
		checkbox1.setOnCheckedChangeListener(new CheckBox.OnCheckedChangeListener() {
					@Override
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						if (checkbox1.isChecked()) {
							DisplayToast("你选择了：" + checkbox1.getText());
						}
					}
				});
		checkbox2.setOnCheckedChangeListener(new CheckBox.OnCheckedChangeListener() {
					@Override
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						if (checkbox2.isChecked()) {
							DisplayToast("你选择了：" + checkbox2.getText());
						}
					}
				});
		checkbox3.setOnCheckedChangeListener(new CheckBox.OnCheckedChangeListener() {
					@Override
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						if (checkbox3.isChecked()) {
							DisplayToast("你选择了：" + checkbox3.getText());
						}
					}

				});
		checkbox4.setOnCheckedChangeListener(new CheckBox.OnCheckedChangeListener() {
					@Override
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						if (checkbox4.isChecked()) {
							DisplayToast("你选择了：" + checkbox4.getText());
						}
					}
				});
		submit.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				int num = 0;
				if (checkbox1.isChecked()) {
					num++;
				}
				if (checkbox2.isChecked()) {
					num++;
				}
				if (checkbox3.isChecked()) {
					num++;
				}
				if (checkbox4.isChecked()) {
					num++;
				}
				DisplayToast("您一共选择了" + num + "款Android手机!");
			}
		});
	}
	public void DisplayToast(String str) {
		Toast toast = Toast.makeText(this, str, Toast.LENGTH_SHORT);
		// 设置Toast的显示位置
		toast.setGravity(Gravity.TOP, 0, 220);
		// 显示Toast
		toast.show();
	}
}
```
main.xml布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/textview"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <CheckBox
        android:id="@+id/checkbox1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/checkbox1" />
    <CheckBox
        android:id="@+id/checkbox2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/checkbox2" />
    <CheckBox
        android:id="@+id/checkbox3"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/checkbox3" />
    <CheckBox
        android:id="@+id/checkbox4"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/checkbox4" />
    <Button
        android:id="@+id/submit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="提交信息" />
</LinearLayout>
```
strings.xml文件
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<string name="hello">调查：你喜欢Android的那款手机？</string>
	<string name="app_name">CheckBoxTest</string>
	<string name="checkbox1">HTC desire HD</string>
	<string name="checkbox2">Google nexus one</string>
	<string name="checkbox3">HTC defy</string>
	<string name="checkbox4">摩托罗拉里程碑II</string>
</resources>
```
运行结果如下：
![img](P)  
![img](P)  
![img](P)  