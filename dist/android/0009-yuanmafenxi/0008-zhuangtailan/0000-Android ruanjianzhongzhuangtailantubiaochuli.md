QQ的状态栏通知机制：当所有QQ的UI Activity切换到后台后，添加状态通知；切换回来后，删除该状态通知。飞信的状态栏通知方式：运行软件后，图标一直显示在状态栏的通知栏中；显示退出软件则删除该状态通知。似乎QQ的更有点技术含量，多个程序切换到后台的处理而已；以飞信的模式，做个类似的测试，案例如下：
程序路径：org.anymobile.im
程序入口：org.anymobile.im.LoginActivity（Action：android.intent.action.MAIN；Category：android.intent.category.LAUNCHER）
主界面程序：org.anymobile.im.MainActivity
测试程序流程：未登录的情况下，或者第一次运行会打开LoginActivity程序；登陆后，一直停留在主界面MainActivity。
所以，通过Notification，需可以回到im项目的上一个界面程序，LoginActivity/MainActivity，这里就要参考Launcher应用的相关实现，Intent的flag设置。
测试代码，新建一个android项目，TestNotification，入口程序：TestActivity，代码如下：
```  
import android.app.Activity;
import android.content.ComponentName;
import android.content.Intent;
import android.graphics.LightingColorFilter;
import android.os.Bundle;
import android.view.Menu;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class TestActivity extends Activity {
	private static final int ADD_ID = 0;
	private static final int DEL_ID = 1;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button button = (Button) this.findViewById(R.id.btn_menu);
		button.setOnClickListener(new OnClickListener() {
			public void onClick(View v) {
				// TestActivity.this.openOptionsMenu();
				String packName = "org.anymobile.im";
				String className = packName + ".LoginActivity";
				Intent intent = new Intent();
				ComponentName componentName = new ComponentName(packName,
						className);
				intent.setComponent(componentName);
				intent.setAction("android.intent.action.MAIN");
				intent.addCategory("android.intent.category.LAUNCHER");
				intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
				intent.addFlags(Intent.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED);
				TestActivity.this.startActivity(intent);
			}
		});
		// button.getBackground().setColorFilter(0xFFFF0000,
		// PorterDuff.Mode.MULTIPLY);
		button.getBackground().setColorFilter(
				new LightingColorFilter(0xFFFFFFFF, 0xFFAA0000));
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		menu.add(0, ADD_ID, 0, "ADD");
		menu.add(0, DEL_ID, 0, "DEL");
		return super.onCreateOptionsMenu(menu);
	}
}
```
OK，开始测试状态栏的通知功能：
1、LoginActivity.onCreate() 调用showNotification()方法，创建一个通知图标；
```  
protected void showNotification() {
	CharSequence from = "IM";
	CharSequence message = "IM start up";
	Intent intent = new Intent();
	ComponentName componentName = new ComponentName("com.longcheer.imm",
			"com.longcheer.imm.activitys.LoginActivity");
	intent.setComponent(componentName);
	intent.setAction("android.intent.action.MAIN");
	intent.addCategory("android.intent.category.LAUNCHER");
	intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	intent.addFlags(Intent.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED);
	PendingIntent contentIntent = PendingIntent.getActivity(this, 0,
			intent, 0);
	Notification notif = new Notification(R.drawable.icon,
			"IMM Still run background!", System.currentTimeMillis());
	notif.setLatestEventInfo(this, from, message, contentIntent);
	// 查一下通知服务
	NotificationManager nm = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
	nm.notify(R.string.app_name, notif);
}
```
2、在LoginActivity / MainAcitivity的退出操作中cancel该通知。
```  
private void doExit() {
	this.finish();
	NotificationManager nm = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
	nm.cancel(R.string.app_name);
}
```