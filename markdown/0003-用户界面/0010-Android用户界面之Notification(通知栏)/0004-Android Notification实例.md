代码如下：
```  
import android.app.Activity;
import android.app.Notification;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.RemoteViews;
public class Main extends Activity {
	private Button btn;// 使用系统的notification布局
	private Button btn1;// 使用自定义布局
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		btn = (Button) findViewById(R.id.btn);
		btn1 = (Button) findViewById(R.id.btn1);
		btn.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showNotification();
			}
		});
		btn1.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				showMyNotification();
			}
		});
	}
	public void showNotification() {
		// 获得系统的NotificationManager对象
		NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
		// 实例化一个的notification
		Notification notification = new Notification(R.drawable.image1, "notice", System.currentTimeMillis());
		Intent intent = new Intent(Main.this, OtherActivity.class);
		// 点击这个notification触发的Intent
		PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent, 1);
		notification.setLatestEventInfo(this, "title", "a message", pendingIntent);
		// 显示notification的布局和触发器
		notificationManager.notify(R.id.btn, notification);
		// 发出通知，根据Id判断是否为新的通知
	}
	public void showMyNotification() {
		NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
		Notification notification = new Notification(R.drawable.icon, "notice", System.currentTimeMillis());
		// 要实现自定义布局，首先自己定义一个 布局文件notification.xml,如放一个ImageView 和
		// TextView，用这个布局文件生成
		// RemoteViews的实例
		RemoteViews contentview = new RemoteViews(getPackageName(), R.layout.notification);
		contentview.setImageViewResource(R.id.image, R.drawable.image1);
		contentview.setTextViewText(R.id.text, "hello world");
		// 将视图赋值给notification
		notification.contentView = contentview;
		notification.contentIntent = pendingIntent;
		// 以上代码代替系统的setLatestEventInfo()方法
		Intent intent = new Intent(Main.this, OtherActivity.class);
		PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent, 1);
		notificationManager.notify(R.id.btn, notification);
	}
}
```