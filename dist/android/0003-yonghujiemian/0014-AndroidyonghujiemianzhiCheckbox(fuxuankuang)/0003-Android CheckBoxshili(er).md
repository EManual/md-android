代码如下：
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.KeyEvent;
import android.view.View;
import android.view.View.OnKeyListener;
import android.widget.*;
import android.widget.CompoundButton.OnCheckedChangeListener;
public class CheckBoxCalc extends Activity {
	private TextView mTextView;
	private TextView mTextView2;
	private TextView mTextView3;
	private TextView mTextView4;
	private CheckBox mBox1;
	private CheckBox mBox2;
	private CheckBox mBox3;
	private CheckBox mBox4;
	private EditText mEditText;
	private EditText mEditText1;
	private boolean isbool = true;
	private OnCheckedChangeListener listner;
	private Float Temp;
	private String Experssion;
	private OnKeyListener list;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mTextView = (TextView) findViewById(R.id.result1);
		mTextView2 = (TextView) findViewById(R.id.result2);
		mTextView3 = (TextView) findViewById(R.id.result3);
		mTextView4 = (TextView) findViewById(R.id.result4);
		mBox1 = (CheckBox) findViewById(R.id.Plus); // 加?乘除
		mBox2 = (CheckBox) findViewById(R.id.Cut);
		mBox3 = (CheckBox) findViewById(R.id.Ride);
		mBox4 = (CheckBox) findViewById(R.id.Except);
		mEditText = (EditText) findViewById(R.id.first);
		mEditText1 = (EditText) findViewById(R.id.second);
		list = new OnKeyListener() {
			@Override
			public boolean onKey(View v, int keyCode, KeyEvent event) {
				if (mBox1.isChecked()) {
					mBox1.setChecked(false);
				}
				if (mBox2.isChecked()) {
					mBox2.setChecked(false);
				}
				if (mBox3.isChecked()) {
					mBox3.setChecked(false);
				}
				if (mBox4.isChecked()) {
					mBox4.setChecked(false);
				}
				return false;
			}
		};
		mEditText.setOnKeyListener(list);
		mEditText1.setOnKeyListener(list);
		listner = new OnCheckedChangeListener() {
			@Override
			public void onCheckedChanged(CompoundButton buttonView,
					boolean isChecked) {
				switch (buttonView.getId()) {
				case R.id.Plus:
					if (!isEmpty(mEditText, mEditText1)) {
						Confirm();
						mBox1.setChecked(false);
						return;
					}
					break;
				case R.id.Cut:
					if (!isEmpty(mEditText, mEditText1)) {
						Confirm();
						mBox2.setChecked(false);
						return;
					}
					break;
				case R.id.Ride:
					if (!isEmpty(mEditText, mEditText1)) {
						Confirm();
						mBox3.setChecked(false);
						return;
					}
					break;
				case R.id.Except:
					if (!isEmpty(mEditText, mEditText1)) {
						Confirm();
						mBox4.setChecked(false);
						return;
					}
					break;
				default:
					break;
				}
				if (mBox1.isChecked()) {
					mTextView.setText(GetOperation("+"));
				} else {
					mTextView.setText("");
				}
				if (mBox2.isChecked()) {
					mTextView2.setText(GetOperation("-"));
				} else {
					mTextView2.setText("");
				}
				if (mBox3.isChecked()) {
					mTextView3.setText(GetOperation("*"));
				} else {
					mTextView3.setText("");
				}
				if (mBox4.isChecked()) {
					mTextView4.setText(GetOperation("/"));
				} else {
					mTextView4.setText("");
				}
			}
		};
		mBox1.setOnCheckedChangeListener(listner);
		mBox2.setOnCheckedChangeListener(listner);
		mBox3.setOnCheckedChangeListener(listner);
		mBox4.setOnCheckedChangeListener(listner);
	}
	public String GetOperation(String Operation) {
		if (Operation == "+") {
			Temp = Float.parseFloat(mEditText.getText().toString())
					+ Float.parseFloat(mEditText1.getText().toString());
		}
		if (Operation == "-") {
			Temp = Float.parseFloat(mEditText.getText().toString())
					- Float.parseFloat(mEditText1.getText().toString());
		}
		if (Operation == "*") {
			Temp = Float.parseFloat(mEditText.getText().toString())
					 * Float.parseFloat(mEditText1.getText().toString());
		}
		if (Operation == "/") {
			Temp = Float.parseFloat(mEditText.getText().toString())
					/ Float.parseFloat(mEditText1.getText().toString());
		}
		Experssion = mEditText.getText().toString() + Operation
				+ mEditText1.getText().toString() + "=" + Temp.toString();
		return Experssion;
	}
	public void Confirm() {
		new AlertDialog.Builder(CheckBoxCalc.this).setTitle("提示")
				.setMessage("??不能?空")
				.setPositiveButton("催定", new DialogInterface.OnClickListener() {
					@Override
					public void onClick(DialogInterface dialog, int which) {
					}
				}).create().show();
	}
	public boolean isEmpty(EditText e, EditText a) {
		if (e.getText().toString().length() > 0
				&& a.getText().toString().length() > 0) {
			isbool = true;
		} else {
			isbool = false;
		}
		return isbool;
	}
}
```