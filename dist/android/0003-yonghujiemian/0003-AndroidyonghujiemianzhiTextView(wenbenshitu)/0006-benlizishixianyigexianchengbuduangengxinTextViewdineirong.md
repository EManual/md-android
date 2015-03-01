关键代码如下：
```  
handler = new Handler() {
	@Override
	public void handleMessage(Message msg) {
		switch (msg.what) {
		case INFO_ID:
			String str = msg.obj.toString();
			textView.append("\n" + str);
			break;
		default:
			break;
		}
		super.handleMessage(msg);
	}
};
Thread thread = new Thread() {
	@Override
	public void run() {
		int flag = 0;
		try {
			while (isRuning) {
				Thread.sleep(1000);
				flag++;
				String str = editText.getText().toString() + "第" + flag
						+ "条";
				Message message = new Message();
				message.what = INFO_ID;
				message.obj = str;
				handler.sendMessage(message);
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
};
thread.start();
```
--------------------------------------------------
Java完整代码如下：HandlerMessageActivity .java
--------------------------------------------------
```  
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
public class HandlerMessageActivity extends Activity {
	TextView textView;
	EditText editText;
	Button button;
	public static final int INFO_ID = 23434;
	private boolean isRuning = true;
	Handler handler;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.handler_layout_main);
		textView = (TextView) this.findViewById(R.id.handler_textview_send);
		editText = (EditText) this.findViewById(R.id.handler_edittext_send);
		button = (Button) this.findViewById(R.id.handler_button_send);
		handler = new Handler() {
			@Override
			public void handleMessage(Message msg) {
				switch (msg.what) {
				case INFO_ID:
					String str = msg.obj.toString();
					textView.append("\n" + str);
					break;

				default:
					break;
				}
				super.handleMessage(msg);
			}
		};
		Thread thread = new Thread() {
			@Override
			public void run() {
				int flag = 0;
				try {
					while (isRuning) {
						Thread.sleep(1000);
						flag++;
						String str = editText.getText().toString() + "第" + flag
								+ "条";
						Message message = new Message();
						message.what = INFO_ID;
						message.obj = str;
						handler.sendMessage(message);
					}
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		};
		thread.start();
	}
	@Override
	protected void onPause() {
		isRuning = false;
		super.onPause();
	}
	@Override
	protected void onResume() {
		isRuning = true;
		super.onResume();
	}
}
```
------------------------------------------
XML布局文件如下：handler_layout_main.xml
------------------------------------------
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <EditText
        android:id="@+id/handler_edittext_send"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <Button
        android:id="@+id/handler_button_send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="200dp"
        android:text="发送数据" />
    <ScrollView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" >
        <TextView
            android:id="@+id/handler_textview_send"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="本activity用于测试handler中message传输" />
    </ScrollView>
</LinearLayout>
```