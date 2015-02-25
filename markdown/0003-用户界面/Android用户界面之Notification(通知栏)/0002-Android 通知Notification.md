Notification是Android中常用的一种通知方式，当有未读短信或者未接电话的时候，屏幕的状态栏就会有提示图标，这时可以下拉状态栏来读取通知。在使用微信的时候（微信在后台运行），如果有新消息时便会发出声音提示，状态栏也有相应的微信提示。
Android中Notification通知的实现步骤：
1.获取NotificationManager对象
NotificationManager的三个公共方法：
```  
①cancel(int id) 取消以前显示的一个通知。假如是一个短暂的通知，试图将隐藏，假如是一个持久的通知，将从状态条中移走。
②cancelAll() 取消以前显示的所有通知。
③notify(int id,  Notification notification) 把通知持久的发送到状态条上。
```
2.初始化Notification对象
Notification的属性：
```  
audioStreamType 当声音响起时，所用的音频流的类型
contentIntent 当通知条目被点击，就执行这个被设置的Intent
contentView 当通知被显示在状态条上的时候，同时这个被设置的视图被显示
defaults 指定哪个值要被设置成默认的
deleteIntent 当用户点击”Clear All Notifications”按钮区删除所有的通知的时候，这个被设置的Intent被执行
icon 状态条所用的图片
iconLevel 假如状态条的图片有几个级别，就设置这里
ledARGB LED灯的颜色
ledOffMS LED关闭时的闪光时间（以毫秒计算）
ledOnMS LED开始时的闪光时间（以毫秒计算）
number 这个通知代表事件的号码
sound 通知的声音
tickerText 通知被显示在状态条时，所显示的信息
vibrate 振动模式
when 通知的时间戳
```
注：如果使Notification常驻在状态栏可以把Notification的flags属性设置为FLAG_ONGOING_EVENT
```  
n.flags = Notification.FLAG_ONGOING_EVENT 
```
3.设置通知的显示参数
使用PendingIntent来包装通知Intent，使用Notification的setLatestEventInfo来设置通知的标题、通知内容等信息。
4.发送通知
使用NotificationManager的notify(int id,  Notification notification)方法来发送通知。
下面代码演示下通知Notification的使用。
Notification实现的步骤：
```  
[Tags]/**
 [Tags]* MainActivity
 [Tags]*/
public class MainActivity extends Activity {
	private Button notifyBtn;
	private Button cancelBtn;
	private NotificationManager nm;
	private Notification n;
	// 通知的ID
	public static final int ID = 1;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		notifyBtn = (Button) findViewById(R.id.notify);
		cancelBtn = (Button) findViewById(R.id.cancel);
		notifyBtn.setOnClickListener(new MyOnClickListener());
		cancelBtn.setOnClickListener(new MyOnClickListener());
		// 1.获取NotificationManager对象
		nm = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
		// 2.初始化Notification对象
		n = new Notification();
		// 设置通知的icon
		n.icon = R.drawable.notify;
		// 设置通知在状态栏上显示的滚动信息
		n.tickerText = "一个通知";
		// 设置通知的时间
		n.when = System.currentTimeMillis();
	}
	class MyOnClickListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			switch (v.getId()) {
			case R.id.notify:
				// 3.设置通知的显示参数
				Intent intent = new Intent(MainActivity.this,
						NotificationView.class);
				PendingIntent pi = PendingIntent.getActivity(MainActivity.this,
						0, intent, 0);
				n.setLatestEventInfo(MainActivity.this, "通知标题", "通知内容", pi);
				// 4.发送通知
				nm.notify(ID, n);
				break;
			case R.id.cancel:
				// 取消通知
				nm.cancel(ID);
				break;
			}
		}
	}
}
```
打开通知后跳转到的Activity：
```  
[Tags]/**
 [Tags]* 打开通知后跳转的Activity
 [Tags]*/
public class NotificationView extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.view);
		// 取消通知
		NotificationManager nm = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
		nm.cancel(MainActivity.ID);
	}
}
```