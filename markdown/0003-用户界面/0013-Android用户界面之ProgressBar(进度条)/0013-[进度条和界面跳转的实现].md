开机画面、加载画面等都可以用下面的例子来实现。
废话不多说，先看效果图：
![img](P)  
最主要的还是Java代码了，其他XML布局，Activity的跳转相信大家都会，主要代码如下：
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.Window;
import android.widget.ProgressBar;
import android.widget.TextView;
public class Yc_welcome extends Activity {
	ProgressBar pb;
	// 消息，用来接受消息和发送消息
	Handler hand;
	int bu = 0;
	TextView tv;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.main);
		// 创建一个线程
		pb = (ProgressBar) findViewById(R.id.progressBar1);
		pb.setMax(100);
		tv = (TextView) findViewById(R.id.texitview2);
		// 定义消息对象
		hand = new Handler() {
			@Override
			public void handleMessage(Message msg) {
				super.handleMessage(msg);
				tv.setText("正在加载:" + bu + "%");
			}
		};
	}
	@Override
	protected void onStart() {
		super.onStart();
		Thread start = new Thread() {
			public void run() {
				try {
					// 让当前线程睡眠40ms
					while (bu < 100) {
						pb.setProgress(bu);
						pb.setSecondaryProgress(bu + 5);
						// 发送消息 Message就是要发送的信息
						// 一般被放到线程中去用来更新UI组件
						Message msg = new Message();
						hand.sendMessage(msg);
						bu++;
						Thread.sleep(40);
					}
					Intent i = new Intent(Yc_welcome.this, thebody.class);
					// 调用一个新的Activity
					startActivity(i);
					// 关闭原本的Activity
					Yc_welcome.this.finish();
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		};
		// 启动线程
		start.start();
		bu = 0;
	}
}
```
相信已经很明白了
PS:
那个加载状态的完成度就是一个TextView，可以去掉的；
长条形的进度条也可以换成圆形的进度条，应用面应该是很广的！
你也可以把进度条都去掉，这样就是一个完美的应用程序的开始Logo画面了。