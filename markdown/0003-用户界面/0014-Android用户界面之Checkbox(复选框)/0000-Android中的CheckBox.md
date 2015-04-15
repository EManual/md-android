CheckBox是一种在界面开发中比较常见的控件，Android中UI开发也有CheckBox，每个CheckBox都要设置监听，设置的监听为CompouButton.OnCheckedChangedListener()。
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.TextView;
public class ChkBoxActivity extends Activity {
	private TextView myTextView;
	private CheckBox myApple;
	private CheckBox myOrange;
	private CheckBox myBanana;
	private CheckBox myWaterMelon;
	private CheckBox myStrawBerry;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 通过ID找到TextView
		myTextView = (TextView) findViewById(R.id.myTextView);
		// 通过ID找到这几个CheckBox
		myApple = (CheckBox) findViewById(R.id.Apple);
		myOrange = (CheckBox) findViewById(R.id.Orange);
		myBanana = (CheckBox) findViewById(R.id.banana);
		myWaterMelon = (CheckBox) findViewById(R.id.watermelon);
		myStrawBerry = (CheckBox) findViewById(R.id.strawberry);
		myApple.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
			@Override
			public void onCheckedChanged(CompoundButton buttonView,
					boolean isChecked) {
				if (isChecked) {
					myTextView.append(myApple.getText().toString());
				}
				else {
					if (myTextView.getText().toString().contains("苹果")) {
						myTextView.setText(myTextView.getText().toString().replace("苹果", ""));
					}
				}
			}
		});
		myOrange.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
			@Override
			public void onCheckedChanged(CompoundButton buttonView,
					boolean isChecked) {
				if (isChecked) {
					myTextView.append(myOrange.getText().toString());
				}
				else {
					if (myTextView.getText().toString().contains("橘子")) {
						myTextView.setText(myTextView.getText().toString().replace("橘子", ""));
					}
				}

			}
		});
		myBanana.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
			@Override
			public void onCheckedChanged(CompoundButton buttonView,
					boolean isChecked) {
				if (isChecked) {
					myTextView.append(myBanana.getText().toString());
				}
				else {
					if (myTextView.getText().toString().contains("香蕉")) {
						myTextView.setText(myTextView.getText().toString()
								.replace("香蕉", ""));
					}
				}
			}
		});
		myWaterMelon.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
					@Override
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						if (isChecked) {
							myTextView.append(myWaterMelon.getText().toString());
						}
						else {
							if (myTextView.getText().toString().contains("西瓜")) {
								myTextView.setText(myTextView.getText().toString().replace("西瓜", ""));
							}
						}
					}
				});
		myStrawBerry.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
					@Override
					public void onCheckedChanged(CompoundButton buttonView,
							boolean isChecked) {
						if (isChecked) {
							myTextView.append(myStrawBerry.getText().toString());
						} else {
							if (myTextView.getText().toString().contains("草莓")) {
								myTextView.setText(myTextView.getText().toString().replace("草莓", ""));
							}
						}
					}
				});
	}
}
```
这里还有XML的代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/myTextView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="请选择你一下你喜欢吃的水果：" />
    <CheckBox
        android:id="@+id/Apple"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="苹果" />
    <CheckBox
        android:id="@+id/Orange"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="橘子" />
    <CheckBox
        android:id="@+id/banana"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="香蕉" />
    <CheckBox
        android:id="@+id/watermelon"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="西瓜" />
    <CheckBox
        android:id="@+id/strawberry"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="草莓" />
</LinearLayout>
```
效果图：
![img](http://emanual.github.io/md-android/img/view_checkbox/01_checkbox.jpg)  