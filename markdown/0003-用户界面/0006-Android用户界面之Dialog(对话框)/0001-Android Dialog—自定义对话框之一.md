还是先从最简单的开始吧，然后一步一步的扩展。
为了保证软件上所谓的低耦合度和可重用性，这里不得不需要单独建立一个类CustomerDialog，然后继承AlertDialog
```  
public class CustomerDialog extends AlertDialog { 
}
```
然后添加一个带Context参数的构造器，context（上下文）通俗点讲一般是指归属于那个,这里就归属于调用的那个Acitivity，也就是说这个对话框是针对调用的那个Activity
```  
public CustomerDialog(Context context) { 
	super(context); 
	this.context = context; 
} 
```
接下来需要对AlertDialog的 onCreate方法覆盖，否则在外面就无法获得你创建的那个自定义对话框的内容了(当然你也可以直接在构造方法里调用setView，当这样一来耦合度就增加了)，然后把自己的自定义内容通过setView关联进去。
```  
@Override 
protected void onCreate(Bundle savedInstanceState) { 
	TextView textView = new TextView(context);
	textView.setText("这是一个自定义对话框");
	textView.setTextSize(24);
	textView.setTextColor(Color.BLACK);
	setView(textView);
	super.onCreate(savedInstanceState); 
} 
```
具体实现：
第一步：
res/layout/main.xml
定义一个button按钮，用来点击后弹出自定义对话框
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/button"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="显示自定义对话框" />
</LinearLayout>
```
第二步：创建自定义对话框类
src/com/dialog/ui/CustomerDialog.java
```  
import android.app.AlertDialog;
import android.content.Context;
import android.graphics.Color;
import android.os.Bundle;
import android.widget.TextView;
public class CustomerDialog extends AlertDialog {
	private Context context = null;
	public CustomerDialog(Context context) {
		super(context);
		this.context = context;
	}
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// 此处仅一个简单的TextView，可以扩展到自定义xml里面
		TextView textView = new TextView(context);
		textView.setText("这是一个自定义对话框");
		textView.setTextSize(24);
		textView.setTextColor(Color.BLACK);
		setView(textView);
		super.onCreate(savedInstanceState);
	}
}
```
第三步：在创建一个Activity
src/com/dialog/activity/CustomerDialogActivity.java
```  
import android.app.Activity;
import android.app.Dialog;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import com.dialog.ui.CustomerDialog;
public class CustomerDialogActivity extends Activity {
	private final int CUSTOMER_DIALOG = 1;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button button = (Button) findViewById(R.id.button);
		View.OnClickListener listener = new View.OnClickListener() {
			@Override
			public void onClick(View view) {
				showDialog(CUSTOMER_DIALOG);
			}
		};
		button.setOnClickListener(listener);
	}
	@Override
	protected Dialog onCreateDialog(int id) {
		CustomerDialog dialog = null;
		switch (id) {
		case CUSTOMER_DIALOG:
			dialog = new CustomerDialog(CustomerDialogActivity.this);
			dialog.setTitle("自定义对话框");
			dialog.setIcon(R.drawable.icon);
			break;
		}
		return dialog;
	}
}
```
上个丑陋的效果图（源码就不提供了）
![img](P)  