代码如下：
```  
public class CallRecord01 extends Activity {
	private Button beginrecordservice;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		beginrecordservice = (Button) findViewById(R.id.startrecordservice);
		beginrecordservice.setOnClickListener(new BeginRecord());
	}
	private class BeginRecord implements OnClickListener {
		@Override
		public void onClick(View v) {
			Intent serviceIntent = new Intent(getApplicationContext(),
					CallRecordService.class);
			getApplicationContext().startService(serviceIntent);
		}
	}
}
public class CallRecordService extends Service {
	@Override
	public IBinder onBind(Intent intent) {
		return null;
	}
	@Override
	public void onCreate() {
		super.onCreate();
		Toast.makeText(getApplicationContext(), "录音服务已经创建!", Toast.LENGTH_LONG)
				.show();
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		Toast.makeText(getApplicationContext(), "录音服务已经销毁!", Toast.LENGTH_LONG)
				.show();
	}
	@Override
	public void onStart(Intent intent, int startId) {
		super.onStart(intent, startId);
		Toast.makeText(getApplicationContext(), "录音服务已经启动!", Toast.LENGTH_LONG)
				.show();
		TelephonyManager telephonymanager = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
		telephonymanager.listen(new PhoneListener(getApplicationContext()),
				PhoneStateListener.LISTEN_CALL_STATE);
	}
}
public class PhoneListener extends PhoneStateListener {
	File audioFile;
	MediaRecorder mediaRecorder; // = new MediaRecorder();
	Context c;
	boolean iscall = false;
	public PhoneListener(Context context) {
		c = context;
		iscall = false;
	}
	@Override
	public void onCallStateChanged(int state, String incomingNumber) {
		super.onCallStateChanged(state, incomingNumber);
		mediaRecorder = new MediaRecorder();
		switch (state) {
		case TelephonyManager.CALL_STATE_OFFHOOK:
			iscall = true;
			try {
				recordCallComment();
			} catch (IOException e) {
				e.printStackTrace();
				mediaRecorder.stop();
			}
			Toast.makeText(c, "正在录音", Toast.LENGTH_SHORT).show();
			break;
		case TelephonyManager.CALL_STATE_IDLE:
			// if(mediaRecorder!=null){
			// mediaRecorder.stop();
			// mediaRecorder=null;
			// }
			if (iscall) {
				mediaRecorder.stop();
				iscall = false;
			}
			break;
		}
	}
	public void recordCallComment() throws IOException {
		System.out.println(mediaRecorder);
		// 这里AudioSource.MIC可以改为AudioSource.VOICE_CALL, 把音源变
		// 电话通话内容, 但似乎很多机都不支持通话录音
		mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
		mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.DEFAULT);
		mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
		audioFile = File.createTempFile("record_", ".amr");
		mediaRecorder.setOutputFile(audioFile.getAbsolutePath());
		mediaRecorder.prepare();
		mediaRecorder.start();
	}
}
```