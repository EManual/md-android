代码如下：
```  
import android.app.Activity;
import android.graphics.Color;
import android.os.Bundle;
import android.text.InputType;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.WindowManager.LayoutParams;
import android.widget.Button;
import android.widget.EditText;
import android.widget.PopupWindow;
import android.widget.TextView;
public class Test1 extends Activity {
	TextView textView;
	Button button;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		button = (Button) findViewById(R.id.button1);
		textView = (TextView) findViewById(R.id.textView1);
		button.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				initPopWindow();
			}
		});
	}
	 /**
	  * 新建一个popupWindow弹出框 popupWindow是一个阻塞式的弹出框，这就意味着在我们退出这个弹出框之前，程序会一直等待，
	  * 这和AlertDialog不同哦，AlertDialog是非阻塞式弹出框，AlertDialog弹出的时候，后台可是还可以做其他事情的哦。
	  */
	private void initPopWindow() {
		// 加载popupWindow的布局文件
		View contentView = LayoutInflater.from(getApplicationContext())
				.inflate(R.layout.popup, null);
		// 设置popupWindow的背景颜色
		contentView.setBackgroundColor(Color.RED);
		// 声明一个弹出框
		final PopupWindow popupWindow = new PopupWindow(
				findViewById(R.id.mainlayout), 200, 300);
		// 为弹出框设定自定义的布局
		popupWindow.setContentView(contentView);
		final EditText editText = (EditText) contentView
				.findViewById(R.id.editText1);
		// 设定当你点击editText时，弹出的输入框是啥样子的。这里设置默认为数字输入哦，这时候你会发现你输入非数字的东西是不行的哦
		editText.setInputType(InputType.TYPE_CLASS_NUMBER);
		/*
		  * 这个popupWindow.setFocusable(true);非常重要，如果不在弹出之前加上这条语句，你会很悲剧的发现，你是无法在
		  * editText中输入任何东西的
		  * 。该方法可以设定popupWindow获取焦点的能力。当设置为true时，系统会捕获到焦点给popupWindow
		  * 上的组件。默认为false哦.该方法一定要在弹出对话框之前进行调用。
		  */
		popupWindow.setFocusable(true);
		/*
		  * popupWindow.showAsDropDown（View view）弹出对话框，位置在紧挨着view组件
		  * showAsDropDown(View anchor, int xoff, int yoff)弹出对话框，位置在紧挨着view组件，x y
		  * 代表着偏移量 showAtLocation(View parent, int gravity, int x, int y)弹出对话框
		  * parent 父布局 gravity 依靠父布局的位置如Gravity.CENTER x y 坐标值
		  */
		popupWindow.showAsDropDown(button);
		Button button_sure = (Button) contentView.findViewById(R.id.button1_sure);
		button_sure.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				popupWindow.dismiss();
				textView.setText("展示信息：" + editText.getText());
			}
		});
		Button button_cancel = (Button) contentView
				.findViewById(R.id.button2_cancel);
		button_cancel.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				popupWindow.dismiss();
			}
		});
	}
}
```
Android中inputType属性在EditText输入值时启动的虚拟键盘的风格有着重要的作用。这也大大的方便的操作。有时需要虚拟键盘只为字符或只为数字。所以inputType尤为重要。
```  
<EditText android:layout_width="fill_parent" android:layout_height="wrap_content" android:inputType="phone" />
	//文本类型，多为大写、小写和数字符号。 
	android:inputType="none"
	android:inputType="text"
	android:inputType="textCapCharacters"
	android:inputType="textCapWords"
	android:inputType="textCapSentences"
	android:inputType="textAutoCorrect"
	android:inputType="textAutoComplete"
	android:inputType="textMultiLine"
	android:inputType="textImeMultiLine"
	android:inputType="textNoSuggestions"
	android:inputType="textUri"
	android:inputType="textEmailAddress"
	android:inputType="textEmailSubject"
	android:inputType="textShortMessage"
	android:inputType="textLongMessage"
	android:inputType="textPersonName"
	android:inputType="textPostalAddress"
	android:inputType="textPassword"
	android:inputType="textVisiblePassword"
	android:inputType="textWebEditText"
	android:inputType="textFilter"
	android:inputType="textPhonetic"
	//数值类型
	android:inputType="number"
	android:inputType="numberSigned"
	android:inputType="numberDecimal"
	android:inputType="phone"//拨号键盘
	android:inputType="datetime
	android:inputType="date"//日期键盘
	android:inputType="time"//时间键盘
```