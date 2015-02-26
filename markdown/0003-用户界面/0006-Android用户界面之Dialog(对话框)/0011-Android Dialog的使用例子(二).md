3、编写资源文件：strings.xml
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<string name="hello">对话框</string>
	<string name="app_name">Android Dialog</string>
	<string name="button01">两个button的对话框</string>
	<string name="button02">三个button的对话框</string>
	<string name="button03">可以进行输入的对话框</string>
	<string name="button04">进度框</string>
	<string name="alert_dialog_two_buttons_title">这是一个提示框，点击取消后可以返回。</string> <string name="alert_dialog_ok">确定</string>
	<string name="alert_dialog_cancle">取消</string>
	<string name="alert_dialog_three_buttons_title">标题部分</string>
	<string name="alert_dialog_three_buttons_msg">对于程序员或创业团队来说，还是有必要拥有一个属于自己的博客。 Wordpress 曾经让个人或企业搭建博客变得非常容易。但是我们觉得 Wordpress 还是有些重量级，所以选择了一个非常轻便的工具 toto，一段只有200多行代码的Ruby应用程序。</string>
	<string name="alert_dialog_something">进入详细</string>
	<string name="alert_diaog_text_entry">请输入</string>
</resources> 
```
4、开发主逻辑代码：DialogTest.java
```  
import com.listeview.R;
import android.app.Activity;
import android.app.AlertDialog;
import android.app.DatePickerDialog;
import android.app.Dialog;
import android.app.ProgressDialog;
import android.app.TimePickerDialog;
import android.app.AlertDialog.Builder;
import android.content.Context;
import android.content.DialogInterface;
import android.os.Bundle;
import android.text.method.CharacterPickerDialog;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class DialogTest extends Activity {
	private static final int dialog4 = 4;
	private static final int dialog3 = 3;
	private static final int dialog2 = 2;
	private static final int dialog1 = 1;
	 /** Called when the activity is first created. */
	private Button button01;
	private Button button02;
	private Button button03;
	private Button button04;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		button01 = (Button) findViewById(R.id.button01);
		button02 = (Button) findViewById(R.id.button02);
		button03 = (Button) findViewById(R.id.button03);
		button04 = (Button) findViewById(R.id.button04);
		button01.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showDialog(dialog1);
			}
		});
		button02.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showDialog(dialog2);
			}
		});
		button03.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showDialog(dialog3);
			}
		});
		button04.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showDialog(dialog4);
			}
		});
	}
	@Override
	protected Dialog onCreateDialog(int id) {
		switch (id) {
		case dialog1:
			return buildDialog1(DialogTest.this);
		case dialog2:
			return buildDialog2(DialogTest.this);
		case dialog3:
			return buildDialog3(DialogTest.this);
		case dialog4:
			return buildDialog4(DialogTest.this);
		}
		return null;
	}
}
```