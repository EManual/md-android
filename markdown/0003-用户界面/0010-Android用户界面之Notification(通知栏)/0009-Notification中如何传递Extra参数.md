希望在手机的状态栏中出现的notification被点击时，启动activity时带上一些extras参数，但是每次都是第一次设定的值，新设定的值不会起作用，通过网上搜索，我找到原因。
原来要把
```  
PendingIntent pi = PendingIntent.getActivity(getApplicationContext(), 0, intent, 0);
```
改为
```  
PendingIntent pi = PendingIntent.getActivity(getApplicationContext(), 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);
```
这样每次新设定的extra机会更新上次的设定值。
例子代码：
```  
public class Test1 extends Activity {
	EditText name;
	EditText id;
	EditText type;
	Notification notification;
	NotificationManager nm;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		nm = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
		notification = new Notification(R.drawable.icon, "notification test",
				System.currentTimeMillis());
		name = (EditText) findViewById(R.id.EditText01);
		id = (EditText) findViewById(R.id.EditText02);
		type = (EditText) findViewById(R.id.EditText03);
		Button btn = (Button) findViewById(R.id.Button01);
		btn.setOnClickListener(new OnClickListener() {
			public void onClick(View arg0) {
				Intent intent = new Intent(Test1.this, Test2.class);
				intent.setFlags(Intent.FILL_IN_DATA);

				intent.putExtra("name", name.getText().toString());
				intent.putExtra("id", id.getText().toString());
				intent.putExtra("type", type.getText().toString());

				PendingIntent pi = PendingIntent.getActivity(
						getApplicationContext(), 0, intent,
						PendingIntent.FLAG_UPDATE_CURRENT);
				notification.setLatestEventInfo(getApplicationContext(),
						"contentTitle", "contentText", pi);
				nm.notify(0, notification);
			}
		});
	}
}
```  
点击notification后触发的类。
```  
public class Test2 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.test2);
		Intent i = getIntent();
		Bundle b = i.getExtras();
		TextView tv = (TextView) findViewById(R.id.TextView01);
		if (b != null) {
			tv.setText(b.getString("name") + " " + b.getString("type") + "
					+ b.getString("id"));
			Log.d("crxcrx", b.getString("name"));
			Log.d("crxcrx", b.getString("type"));
			Log.d("crxcrx", b.getString("id"));
		}
	}
}
```