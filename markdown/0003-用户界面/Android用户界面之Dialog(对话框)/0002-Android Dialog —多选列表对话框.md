和单选列表对话框相似，这里需要通过setMultiChoiceItems将array.xml中的数据添加进去。
当单击列表项时会产生Click事件，这里用到的监听器是DialogInterface.OnMultiChoiceClickListener，具体实现如下：
第一步：
添加res/values/array.xml的数据
```   
<?xml version="1.0" encoding="utf-8"?>  
<resources>  
    <string-array name="hobby">
        <item>篮球</item>
        <item>足球</item>
        <item>排球</item>
    </string-array>
</resources> 
```
第二步：一个输入框和一个按钮
res/layout/muti_choice_dialog_layout.xml
```   
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout  
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content">
  <EditText android:id="@+id/editText"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="这是一个多选列表对话框"
  />
  <Button android:id="@+id/button"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="显示多选列表对话框"
  />
</LinearLayout> 
```
第三步：
src/com/dialog/activity/MutiChoiceDialogActivity.java
```   
import android.app.Activity;
import android.app.AlertDialog;
import android.app.AlertDialog.Builder;
import android.app.Dialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
public class MutiChoiceDialogActivity extends Activity {
	private final int MUTI_CHOICE_DIALOG = 1;
	boolean[] selected = new boolean[] { false, false, false};
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.muti_choice_dialog_layout);
		Button button = (Button) findViewById(R.id.button);
		View.OnClickListener listener = new View.OnClickListener() {
			@Override
			public void onClick(View view) {
				showDialog(MUTI_CHOICE_DIALOG);
			}
		};
		button.setOnClickListener(listener);
	}
	@Override
	protected Dialog onCreateDialog(int id) {
		Dialog dialog = null;
		switch (id) {
		case MUTI_CHOICE_DIALOG:
			Builder builder = new AlertDialog.Builder(this);
			builder.setTitle("多选列表对话框");
			builder.setIcon(R.drawable.basketball);
			DialogInterface.OnMultiChoiceClickListener mutiListener = new DialogInterface.OnMultiChoiceClickListener() {
				@Override
				public void onClick(DialogInterface dialogInterface, int which,
						boolean isChecked) {
					selected[which] = isChecked;
				}
			};
			builder.setMultiChoiceItems(R.array.hobby, selected, mutiListener);
			DialogInterface.OnClickListener btnListener = new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialogInterface, int which) {
					String selectedStr = "";
					for (int i = 0; i < selected.length; i++) {
						if (selected == true) {
							selectedStr = selectedStr
									+ "
									+ getResources().getStringArray(
											R.array.hobby);
						}
					}
					EditText editText = (EditText) findViewById(R.id.editText);
					editText.setText(selectedStr);
				}
			};
			builder.setPositiveButton("确定", btnListener);
			dialog = builder.create();
			break;
		}
		return dialog;
	}
}
```
![img](P)  