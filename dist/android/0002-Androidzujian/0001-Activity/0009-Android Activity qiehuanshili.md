第一个activity.
Java代码：
```  
import android.R;
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class Activity1 extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity1);
		Button button = (Button) findViewById(R.id.button1);
		button.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				Intent intent = new Intent();
				intent.setClass(Activity1.this, Activity2.class);
				startActivity(intent);
				Activity1.this.finish();
			}
		});
	}
}
```
第二个activity.上面的代码主要是写从一个activity到另一个activity,大家一定要记住的就是在Internet.setClass后面的括号里，逗号前面的是写当前界面，逗号后面的是写你要跳转界面的。
```  
import android.R;
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class Activity2 extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity2);
		Button button = (Button) findViewById(R.id.button2);
		button.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				Intent intent = new Intent();
				intent.setClass(Activity2.this, Activity1.class);
				startActivity(intent);
				Activity2.this.finish();
			}
		});
	}
}
```