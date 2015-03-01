EditText正则表达式的使用 检查输入是否符合规则 
```  
import Android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
 /**
  * Class which shows how to validate user input with regular expression
  */
public class Main extends Activity {
	private EditText editText;
	private Button button;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		editText = (EditText) findViewById(R.id.textId);
		editText.setText("EditText element");
		button = (Button) findViewById(R.id.btnId);
		button.setText("Check");
		button.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				if (checkString(editText.getText().toString())) {
					editText.setText("Corect");
				}
			}
		});
	}
	 /**
	  * This method checks if String is correct
	  * @param s
	  *            - String which need to check
	  * @return value of matching
	  */
	private boolean checkString(String s) {
		return s.matches("\\w*[.](Java|cpp|class)");
	}
}
String s_Result = "Distance: 2.8km (about 9 mins)";
// Distance parsing
Pattern p = Pattern.compile("Distance: (\\d+(\\.\\d+)?)(.*?)\\b");
Matcher m = p.matcher(s_Result);
if (m.find()) {
	MatchResult mr = m.toMatchResult();
	f_Distance = mr.group(1);// 2.8
	m_DistanceUnit = mr.group(3);// km
}
// Time parsing
p = Pattern.compile("about (\\d+(\\.\\d+)?) (.*)\\b");
m = p.matcher(s_Result);
if (m.find()) {
	MatchResult mr = m.toMatchResult();
	f_timeEst = mr.group(1);// 9
	m_timeEstUnit = mr.group(3);// min
}
```