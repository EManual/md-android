通过对Handler的学习，使我们理解了线程的运行，当我们使用多个线程的时候，会感觉很乱，如果我们应用Handler，我们可以更加清晰的去进行更新。下面是一个规定时间自动更新Title的示例：
```  
import java.util.Timer;
import java.util.TimerTask;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
public class HandlerDemo extends Activity {
	// title为setTitle方法提供变量,这里为了方便我设置成了int型
	private int title = 0;
	private Handler mHandler = new Handler() {
		public void handleMessage(Message msg) {
			switch (msg.what) {
			case 1:
				updateTitle();
				break;
			}
		};
	};
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);

		Timer timer = new Timer();
		timer.scheduleAtFixedRate(new MyTask(), 1, 5000);
	}
	private class MyTask extends TimerTask {
		@Override
		public void run() {

			Message message = new Message();
			message.what = 1;
			mHandler.sendMessage(message);
		}
	}
	public void updateTitle() {

		setTitle("Welcome to Mr Wei's blog " + title);
		title++;
	}
}
```
