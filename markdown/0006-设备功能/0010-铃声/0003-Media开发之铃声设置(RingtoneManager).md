代码如下：
```  
public class Media_RingTongActivity extends Activity {
	// 定义三个按钮
	private Button mRingtongButton;
	private Button mAlarmButton;
	private Button mNotificationButton;
	// 定义类型
	private static final int RingtongButton = 0;
	private static final int AlarmButton = 1;
	private static final int NotificationButton = 2;
	// 铃声文件夹
	private String strRingtongFolder = "/sdcard/media/ringtones";
	private String strAlarmFolder = "/sdcard/media/alarms";
	private String strNotificationFolder = "/sdcard/media/notifications";
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mRingtongButton = (Button) findViewById(R.id.myRingtongButton);
		mRingtongButton.setOnClickListener(new myRingtongButtonListener());
		mAlarmButton = (Button) findViewById(R.id.myAlarmButton);
		mAlarmButton.setOnClickListener(new myAlarmButtonListener());
		mNotificationButton = (Button) findViewById(R.id.myNotificationButton);
		mNotificationButton
				.setOnClickListener(new myNotificationButtonListener());
	}
	// 设置来电铃声监听器
	private class myRingtongButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			if (isFolder(strRingtongFolder)) {
				// 打开系统铃声设置
				Intent intent = new Intent(
						RingtoneManager.ACTION_RINGTONE_PICKER);
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_PICKED_URI, true);
				// 类型为来电ringtong
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TYPE,
						RingtoneManager.TYPE_RINGTONE);
				// 设置显示的题目
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TITLE, "设置来电的铃声");
				// 当设置完成之后返回到当前的activity
				startActivityForResult(intent, RingtongButton);
			}
		}
	}
	// 设置闹钟铃声监听器
	private class myAlarmButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			if (isFolder(strAlarmFolder)) {
				Intent intent = new Intent(
						RingtoneManager.ACTION_RINGTONE_PICKER);
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TYPE,
						RingtoneManager.TYPE_ALARM);
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TITLE, "设置闹铃铃声");
				startActivityForResult(intent, AlarmButton);
			}
		}
	}
	// 设置通知铃声监听器
	private class myNotificationButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			if (isFolder(strNotificationFolder)) {
				Intent intent = new Intent(
						RingtoneManager.ACTION_RINGTONE_PICKER);
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TYPE,
						RingtoneManager.TYPE_NOTIFICATION);
				intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TITLE, "设置通知铃声");
				startActivityForResult(intent, NotificationButton);
			}
		}
	}
	// 检查是否存在指定的文件夹，如果不存在就创建
	private boolean isFolder(String strFolder) {
		boolean tmp = false;
		File f1 = new File(strFolder);
		if (!f1.exists()) {
			if (f1.mkdirs()) {
				tmp = true;
			} else {
				tmp = false;
			}
		} else {
			tmp = true;
		}
		return tmp;
	}
	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		if (resultCode != RESULT_OK) {
			return;
		}
		switch (requestCode) {
		case RingtongButton:
			try {
				// 得到我们选择的铃声
				Uri pickedUri = data
						.getParcelableExtra(RingtoneManager.EXTRA_RINGTONE_PICKED_URI);
				// 将我们选择的铃声选择成默认
				if (pickedUri != null) {
					RingtoneManager.setActualDefaultRingtoneUri(
							Media_RingTongActivity.this,
							RingtoneManager.TYPE_RINGTONE, pickedUri);
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
			break;
		case AlarmButton:
			try {
				// 得到我们选择的铃声
				Uri pickedUri = data
						.getParcelableExtra(RingtoneManager.EXTRA_RINGTONE_PICKED_URI);
				// 将我们选择的铃声选择成默认
				if (pickedUri != null) {
					RingtoneManager.setActualDefaultRingtoneUri(
							Media_RingTongActivity.this,
							RingtoneManager.TYPE_ALARM, pickedUri);
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
			break;
		case NotificationButton:
			try {
				// 得到我们选择的铃声
				Uri pickedUri = data
						.getParcelableExtra(RingtoneManager.EXTRA_RINGTONE_PICKED_URI);
				// 将我们选择的铃声选择成默认
				if (pickedUri != null) {
					RingtoneManager.setActualDefaultRingtoneUri(
							Media_RingTongActivity.this,
							RingtoneManager.TYPE_NOTIFICATION, pickedUri);
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
			break;
		}
		super.onActivityResult(requestCode, resultCode, data);
	}
}
```
1.布局文件就是三个按钮，没什么好说的了。 
2.在真机盖世兔上测试了一下，可以运行，在模拟器测试的童鞋要注意了，每次把音频文件push到sdcard中得时候,必须重启模拟器,模拟器才会应用设置,不然是检索不到的哦，这点我后面才发现的。
3.系统的原始声音存放在/system/media/audio/文件中、
4.最后一点不要忘记给应用程序加权限了：
```  
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
<uses-permission android:name="android.permission.WRITE_SETTINGS"/>
```
结果：
![img](P)  