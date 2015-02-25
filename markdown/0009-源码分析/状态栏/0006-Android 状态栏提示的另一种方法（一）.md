#### NotificationManager与Notification对象的应用
在Android手机界面的最上方有一条显示时间、信号强度和电池状态等信息的区域，这是Android手机的状态栏（Status Bar）。当系统有一些信息要通知手机用户时，例如，收到新短信、收到新的电子邮件或有未接来电时，系统通常会把信息显示在状态栏中，有的仅显示小图标，有的则显示图标及文字提醒，用手指按住状态栏往下拉，还可以展开状态栏，查看所有系统发出的信息。
在程序中，要如何把提示信息放入状态栏，又要如何显示小图标呢？Android API已经为了管理提示信息（Notification），率先定义了NotificationManager（Android.app.NotificationMana- ger），只要将Notification添加NotificationManager，即可将信息显示在状态栏。
本范例将模拟MSN在线状态的切换，会在切换在线状态的同时，改变状态栏上显示的在线状态小图标，并以文字提示目前MSN显示的状态为何。
实现本范例时需先准备几张MSN在线状态的小Icon，并预先存入/res/drawable/文件夹中，图片文件路径如下：
```  
/res/drawable/msn.png：    在线的小ICON 
/res/drawable/away.png：   离开的小ICON 
/res/drawable/busy.png：   忙碌中的小ICON 
/res/drawable/min.png：    马上回来的小ICON 
/res/drawable/offine.png： 离线的小ICON
```
效果图:
![img](P)  
程序中以setAdapter()将存放5种登录状态的String[] status设置至Spinner中，当任何一个item被选择时，将会触发onItemSelected()这个方法，并依照其所选择的item，调用自定义的setNotiType()来发出Notification。
在setNotiType()这个方法中，以NotificationManager.notify()来发出Notification，并同时发出系统默认的铃响。
```  
import android.app.Notification;
import android.app.NotificationManager;
import android.app.PendingIntent;
public class EX05_08 extends Activity {
	/* 声明对象变量 */
	private NotificationManager myNotiManager;
	private Spinner mySpinner;
	private ArrayAdapter<String> myAdapter;
	private static final String[] status = { "在线", "离开", "忙碌中", "马上回来", "离线" };
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		/* 载入main.xml Layout */
		setContentView(R.layout.main);
		/* 初始化对象 */
		myNotiManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
		mySpinner = (Spinner) findViewById(R.id.mySpinner);
		myAdapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_spinner_item, status);
		/* 应用myspinner_dropdown自定义下拉菜单模式 */
		myAdapter.setDropDownViewResource(R.layout.myspinner_dropdown);
		/* 将ArrayAdapter添加Spinner对象中 */
		mySpinner.setAdapter(myAdapter);
		/* 将mySpinner添加OnItemSelectedListener */
		mySpinner
				.setOnItemSelectedListener(new Spinner.OnItemSelectedListener() {
					@Override
					public void onItemSelected(AdapterView<?> arg0, View arg1,
							int arg2, long arg3) {
						/* 依照选择的item来判断要发哪一个notification */
						if (status[arg2].equals("在线")) {
							setNotiType(R.drawable.msn, "在线");
						} else if (status[arg2].equals("离开")) {
							setNotiType(R.drawable.away, "离开");
						} else if (status[arg2].equals("忙碌中")) {
							setNotiType(R.drawable.busy, "忙碌中");
						} else if (status[arg2].equals("马上回来")) {
							setNotiType(R.drawable.min, "马上回来");
						} else {
							setNotiType(R.drawable.offine, "离线");
						}
					}
					@Override
					public void onNothingSelected(AdapterView<?> arg0) {
					}
				});
	}
	/* 发出Notification的method */
	private void setNotiType(int iconId, String text) {
		/*
		 [Tags]* 创建新的Intent，作为单击Notification留言条时， 会运行的Activity
		 [Tags]*/
		Intent notifyIntent = new Intent(this, EX05_08_1.class);
		notifyIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
		/* 创建PendingIntent作为设置递延运行的Activity */
		PendingIntent appIntent = PendingIntent.getActivity(EX05_08.this, 0,
				notifyIntent, 0);
		/* 创建Notication，并设置相关参数 */
		Notification myNoti = new Notification();
		/* 设置statusbar显示的icon */
		myNoti.icon = iconId;
		/* 设置statusbar显示的文字信息 */
		myNoti.tickerText = text;
		/* 设置notification发生时同时发出默认声音 */
		myNoti.defaults = Notification.DEFAULT_SOUND;
		/* 设置Notification留言条的参数 */
		myNoti.setLatestEventInfo(EX05_08.this, "MSN登录状态", text, appIntent);
		/* 送出Notification */
		myNotiManager.notify(0, myNoti);
	}
}
```