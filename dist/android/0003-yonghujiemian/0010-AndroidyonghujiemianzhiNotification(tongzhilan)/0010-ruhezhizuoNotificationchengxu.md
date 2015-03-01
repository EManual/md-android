先给大家看一下什么是Notification：
其实就是我们智能手机上显示的那些电量、QQ消息之类的置顶的小图标。制作起来也不难，用到的关键类是NotificationManager。接下来展示一下代码：
```  
public class NotificationActivity extends Activity {
	// 申明一个NotificationManager 对象
	private NotificationManager n;// spinner对象，想必大家都比较了解这个对象的作用了，就是显示出来各种状态，比如在线、离线等七种
	private Spinner s;// 这个adapter想必大家也不陌生，就是一个适配器，给spinner提供内容
	private ArrayAdapter<String> adapter;// 用string[] 来存放其中状态
	private String[] status = { "离开", "离线", "忙碌中", "请勿打扰", "Q我吧", "隐身", "在线" };
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 实例化NotificationManager对象，注意实例化的方法
		n = (NotificationManager) getSystemService(NOTIFICATION_SERVICE); // 实例化spinner对象，这个spinner是一个控件
		s = (Spinner) findViewById(R.id.mySpinner); // 实例化适配器对象，应该也是比较基础的了
		adapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_spinner_item, status); // 为适配器设置下拉菜单的样式，也是基础的
		adapter.setDropDownViewResource(R.layout.myspinner_dropdown); // 为spinner对象设置适配器
		s.setAdapter(adapter); // 为spinner设置按钮监听
		s.setOnItemSelectedListener(new Spinner.OnItemSelectedListener() {
			public void onItemSelected(AdapterView<?> arg0, View arg1, int a,
					long arg3) {
				if (status[a].equals("离开")) {
					setNotifi(R.drawable.likai, "离开");
				} else if (status[a].equals("离线")) {
					setNotifi(R.drawable.lixian, "离线");
				} else if (status[a].equals("忙碌中")) {
					setNotifi(R.drawable.mangluzhong, "忙碌中");
				} else if (status[a].equals("请勿打扰")) {
					setNotifi(R.drawable.qingwudarao, "请勿打扰");
				} else if (status[a].equals("Q我吧")) {
					setNotifi(R.drawable.qwoba, "Q我吧");
				} else if (status[a].equals("隐身")) {
					setNotifi(R.drawable.yinshen, "隐身");
				} else if (status[a].equals("在线")) {
					setNotifi(R.drawable.zaixian, "在线");
				}
			}
			public void onNothingSelected(AdapterView<?> arg0) {
			}
		});
	}// 最关键得 方法：当用户选择了一个状态以后就会调用这个方法，让系统做出相应的反应
	private void setNotifi(int icon, String text) {
		// 这里的Intent是用于当我们生成了Notification，并在手机顶端下拉，再点击这个状态的时候，我们需要让系统做出的反应，而且，这一步是必须构造的，因为接下来你就会发现，我们要让Notification
		// 去工作的话就需要这个notifyIntent
		Intent notifyIntent = new Intent(this, EX05_08_1.class);
		notifyIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
		PendingIntent appIntent = PendingIntent.getActivity(
				NotificationActivity.this, 0, notifyIntent, 0);
		Notification notifi = new Notification();// 为Notification 对象设置图标
		notifi.icon = icon;// 为Notification 对象设置需要显示消息
		notifi.tickerText = text;// 为Notification 设置声音
		notifi.defaults = Notification.DEFAULT_SOUND;
		notifi.setLatestEventInfo(NotificationActivity.this, "QQ登陆状态", text,
				appIntent);
		n.notify(0, notifi);
	}
}
```