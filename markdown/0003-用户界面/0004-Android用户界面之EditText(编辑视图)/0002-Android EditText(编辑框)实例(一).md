java.lang.Object 
android.view.View 
android.widget.TextView 
android.widget.EditText 
EditText是可编辑的文本框。
在用户没有输入的时候，我们默认在编辑框中显示“请输入数字”的提示，要实现这一功能很简单，秩序哟啊“EditText.setHint("请输入数字")”或者在XML布局文件上写上“android:hint="请输入数字"”即可。
下面通过一个简单的计算器来说明EditText的使用
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;
public class Calculater extends Activity {
	 /** Called when the activity is first created. */
	private EditText numberone;
	private EditText numbertwo;
	private Button mybutton;
	private TextView symbol;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		numberone = (EditText) findViewById(R.id.numberone);
		numbertwo = (EditText) findViewById(R.id.numbertwo);
		mybutton = (Button) findViewById(R.id.mybutton);
		symbol = (TextView) findViewById(R.id.symbol);
		mybutton.setOnClickListener(new Calculate());
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		menu.add(0, 1, 1, R.string.exit);
		menu.add(0, 2, 2, R.string.about);
		return super.onCreateOptionsMenu(menu);
	}
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		if (item.getItemId() == 1) {
			finish();
		} else if (item.getItemId() == 2) {
			Toast.makeText(this, "亲爱的你可知，我有多么思念你！", Toast.LENGTH_SHORT).show();
		}
		return super.onOptionsItemSelected(item);
	}
	class Calculate implements OnClickListener {
		@Override
		public void onClick(View v) {
			String number1 = numberone.getText().toString();
			String number2 = numbertwo.getText().toString();
			Intent intent = new Intent();
			intent.putExtra("num1", number1);
			intent.putExtra("num2", number2);
			intent.setClass(Calculater.this, Result.class);
			Calculater.this.startActivity(intent);
		}
	}
}
```
Result.java源文件
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.widget.TextView;
public class Result extends Activity {
	private TextView textview;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.result);
		textview = (TextView) findViewById(R.id.textview);
		Intent intent = getIntent();
		String numberone = intent.getStringExtra("num1");
		String numbertwo = intent.getStringExtra("num2");
		Double d1 = Double.parseDouble(numberone);
		Double d2 = Double.parseDouble(numbertwo);
		Double result = d1 / d2;
		textview.setText(numberone + " / " + numbertwo + " = " + result);
	}
}
```