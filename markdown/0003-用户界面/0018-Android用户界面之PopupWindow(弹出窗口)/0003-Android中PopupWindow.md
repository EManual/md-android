PopupWindow这种对话框。PopupWindow是阻塞对话框，只有在外部线程或者PopupWindow本身做退出操作才行。PopupWindow完全依赖Layout做外观，在常见的开发中，PopupWindow应该会与AlertDialog常混用。
这里的PopupWindow是个登录框，点“确定”则自动填写，点“取消”则关闭PopupWindow。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:background="000000"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/username_view"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:text="用户名"
        android:textAppearance="?android:attr/textAppearanceMedium" />
    <EditText
        android:id="@+id/username_edit"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:capitalize="none"
        android:textAppearance="?android:attr/textAppearanceMedium" />
    <TextView
        android:id="@+id/password_view"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:text="密码"
        android:textAppearance="?android:attr/textAppearanceMedium" />
    <EditText
        android:id="@+id/password_edit"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="20dip"
        android:layout_marginRight="20dip"
        android:capitalize="none"
        android:password="true
        android:textAppearance="?android:attr/textAppearanceMedium" />
    <LinearLayout
        android:id="@+id/LinearLayout01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center" >
        <Button
            android:id="@+id/BtnOK"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="100"
            android:text="确定" >
        </Button>
        <Button
            android:id="@+id/BtnCancel"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="100"
            android:text="取消" >
        </Button>
    </LinearLayout>
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.content.Context;
import android.content.DialogInterface;
import android.os.Bundle;
import android.text.Editable;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.PopupWindow;
public class testAlertDialog extends Activity {
	Button btnPopupWindow;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 定义按钮
		btnPopupWindow = (Button) this.findViewById(R.id.Button01);
		btnPopupWindow.setOnClickListener(new ClickEvent());
	}
	// 统一处理按键事件
	class ClickEvent implements OnClickListener {
		@Override
		public void onClick(View v) {
			if (v == btnPopupWindow) {
				showPopupWindow(testAlertDialog.this,
						testAlertDialog.this.findViewById(R.id.Button01));
			}
		}
	}
	public void showPopupWindow(Context context, View parent) {
		LayoutInflater inflater = (LayoutInflater) context
				.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
		final View vPopupWindow = inflater.inflate(R.layout.popupwindow, null, false);
		final PopupWindow pw = new PopupWindow(vPopupWindow, 300, 300, true);
		// OK按钮及其处理事件
		Button btnOK = (Button) vPopupWindow.findViewById(R.id.BtnOK);
		btnOK.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				// 设置文本框内容
				EditText edtUsername = (EditText) vPopupWindow
						.findViewById(R.id.username_edit);
				edtUsername.setText("username");
				EditText edtPassword = (EditText) vPopupWindow
						.findViewById(R.id.password_edit);
				edtPassword.setText("password");
			}
		});
		// Cancel按钮及其处理事件
		Button btnCancel = (Button) vPopupWindow.findViewById(R.id.BtnCancel);
		btnCancel.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				pw.dismiss();// 关闭
			}
		});
		// 显示popupWindow对话框
		pw.showAtLocation(parent, Gravity.CENTER, 0, 0);
	}
}
```