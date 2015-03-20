1、将TextView装载到列表项中去就可以实现带图标的列表项，这里需要借助BaseAdapter适配器来实现，然后通过getView将TextView返回就OK。
设置图片资源到TextView需要用到setCompoundDrawable(left,top,right,bottom)此方法，如下:
```  
textView.setCompoundDrawablesWithIntrinsicBounds(imgIds[position], 0, 0, 0);  
textView.setCompoundDrawablesWithIntrinsicBounds(imgIds[position], 0, 0, 0); 
```
imgIds是图片资源数组,即将图片资源设置到TextView的左边（文字在右边，相对位置为文字）
具体设置TextView如下：
```  
TextView textView = new TextView(IconListDialogActivity.this);
// 获得array.xml中的数组资源getStringArray返回的是一个String数组
String text = getResources().getStringArray(R.array.hobby)[position];
textView.setText(text);
// 设置字体大小
textView.setTextSize(24);
AbsListView.LayoutParams layoutParams = new AbsListView.LayoutParams(
		LayoutParams.FILL_PARENT, LayoutParams.WRAP_CONTENT);
textView.setLayoutParams(layoutParams);
// 设置水平方向上居中
textView.setGravity(android.view.Gravity.CENTER_VERTICAL);
textView.setMinHeight(65);
// 设置文字颜色
textView.setTextColor(Color.BLACK);
// 设置图标在文字的左边
textView.setCompoundDrawablesWithIntrinsicBounds(imgIds[position], 0, 0, 0);
// 设置textView的左上右下的padding大小
textView.setPadding(15, 0, 15, 0);
// 设置文字和图标之间的padding大小
textView.setCompoundDrawablePadding(15);
TextView textView = new TextView(IconListDialogActivity.this);
// 获得array.xml中的数组资源getStringArray返回的是一个String数组
String text = getResources().getStringArray(R.array.hobby)[position];
textView.setText(text);
// 设置字体大小
textView.setTextSize(24);
AbsListView.LayoutParams layoutParams = new AbsListView.LayoutParams(
		LayoutParams.FILL_PARENT, LayoutParams.WRAP_CONTENT);
textView.setLayoutParams(layoutParams);
// 设置水平方向上居中
textView.setGravity(android.view.Gravity.CENTER_VERTICAL);
textView.setMinHeight(65);
// 设置文字颜色
textView.setTextColor(Color.BLACK);
// 设置图标在文字的左边
textView.setCompoundDrawablesWithIntrinsicBounds(imgIds[position], 0, 0, 0);
// 设置textView的左上右下的padding大小
textView.setPadding(15, 0, 15, 0);
// 设置文字和图标之间的padding大小
textView.setCompoundDrawablePadding(15);
```
关于BaseAdapter具体用法和对话框原理，参考spinner下拉列表和普通对话框原理
2、模拟菜单项带图标
对于菜单的子菜单项，无论是上下文菜单ContextMenu还是SubMenu都不支持图片资源，这里针对输入框的上下文菜单的简单模拟(其他类似)。输入框长按将弹出菜单，故需要对输入框长按事件监听，如下
```  
EditText editText = (EditText) findViewById(R.id.editText);
View.OnLongClickListener editListener = new View.OnLongClickListener() {
	@Override
	public boolean onLongClick(View view) {
		showDialog(ICON_LIST_DIALOG);
		return true;
	}
};
editText.setOnLongClickListener(editListener);
EditText editText = (EditText) findViewById(R.id.editText);
View.OnLongClickListener editListener = new View.OnLongClickListener() {
	@Override
	public boolean onLongClick(View view) {
		showDialog(ICON_LIST_DIALOG);
		return true;
	}
};
editText.setOnLongClickListener(editListener);
```
实现步骤
第一步：定义res/values/array.xml 用来存放列表项文字内容
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
第二步：接下来还是一个输入框和一个按钮，如下
res/layout/icon_list_dialog_layout.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <EditText
        android:id="@+id/editText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="长按输入框将弹出上下文菜单ContextMenu" />
    <Button
        android:id="@+id/button"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="显示列表对话框 " />
</LinearLayout> 
```
第三步：src/com/myiconlistdialog/activity/IconListDialogActivity.java
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.app.Dialog;
import android.app.AlertDialog.Builder;
import android.content.DialogInterface;
import android.graphics.Color;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup;
import android.view.ViewGroup.LayoutParams;
import android.widget.AbsListView;
import android.widget.BaseAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
public class IconListDialogActivity extends Activity {
	private int[] imgIds = { R.drawable.basketball, R.drawable.football,
			R.drawable.volleyball };
	private final int ICON_LIST_DIALOG = 1;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.icon_list_dialog_layout);
		View.OnClickListener btnListener = new View.OnClickListener() {

			@Override
			public void onClick(View view) {
				showDialog(ICON_LIST_DIALOG);
			}
		};
		button.setOnClickListener(btnListener);
		EditText editText = (EditText) findViewById(R.id.editText);
		View.OnLongClickListener editListener = new View.OnLongClickListener() {
			@Override
			public boolean onLongClick(View view) {
				showDialog(ICON_LIST_DIALOG);
				return true;
			}
		};
		editText.setOnLongClickListener(editListener);
	}
	@Override
	protected Dialog onCreateDialog(int id) {
		Dialog dialog = null;
		switch (id) {
		case ICON_LIST_DIALOG:
			Builder builder = new AlertDialog.Builder(this);
			builder.setIcon(R.drawable.basketball);
			builder.setTitle("体育爱好");
			BaseAdapter adapter = new ListItemAdapter();
			DialogInterface.OnClickListener listener = new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialogInterface, int which) {
					EditText editText = (EditText) findViewById(R.id.editText);
					editText.setText("你选择了:
							+ getResources().getStringArray(R.array.hobby)[which]);
				}
			};
			builder.setAdapter(adapter, listener);
			dialog = builder.create();
			break;
		}
		return dialog;
	}
	class ListItemAdapter extends BaseAdapter {
		@Override
		public int getCount() {
			return imgIds.length;
		}
		@Override
		public Object getItem(int position) {
			return null;
		}
		@Override
		public long getItemId(int position) {
			return 0;
		}
		@Override
		public View getView(int position, View contentView, ViewGroup parent) {
			TextView textView = new TextView(IconListDialogActivity.this);
			// 获得array.xml中的数组资源getStringArray返回的是一个String数组
			String text = getResources().getStringArray(R.array.hobby)[position];
			textView.setText(text);
			// 设置字体大小
			textView.setTextSize(24);
			AbsListView.LayoutParams layoutParams = new AbsListView.LayoutParams(
					LayoutParams.FILL_PARENT, LayoutParams.WRAP_CONTENT);
			textView.setLayoutParams(layoutParams);
			// 设置水平方向上居中
			textView.setGravity(android.view.Gravity.CENTER_VERTICAL);
			textView.setMinHeight(65);
			// 设置文字颜色
			textView.setTextColor(Color.BLACK);
			// 设置图标在文字的左边
			textView.setCompoundDrawablesWithIntrinsicBounds(imgIds[position],
					0, 0, 0);
			// 设置textView的左上右下的padding大小
			textView.setPadding(15, 0, 15, 0);
			// 设置文字和图标之间的padding大小
			textView.setCompoundDrawablePadding(15);
			return textView;
		}
	}
}
```
![img](http://emanual.github.io/md-android/img/view_dialog/04_dialog.jpg) 