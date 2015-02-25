java代码：
```  
public class CountService extends Service {
	private boolean threadDisable;
	private String activityValue = null;
	private int count;
	@Override
	public IBinder onBind(Intent intent) {
		Log.v("CountService", "on bind");
		return null;
	}
	@Override
	public void onCreate() {
		super.onCreate();
		Log.v("CountService", "on create");
		new Thread(new Runnable() {
			@Override
			public void run() {
				while (!threadDisable) {
					try {
						Thread.sleep(1000);
					} catch (InterruptedException e) {
					}
					count++;
					Log.v("CountService", "Count is " + count);
				}
			}
		}).start();
	}
	@Override
	public void onRebind(Intent intent) {
		// TODO Auto-generated method stub
		super.onRebind(intent);
		Log.v("CountService", "on rebind");
	}
	@Override
	public void onStart(Intent intent, int startId) {
		// TODO Auto-generated method stub
		super.onStart(intent, startId);
		Log.v("CountService", "on start");
		// 接收activity传递过来的值
		Bundle b = intent.getExtras();
		activityValue = b.getString("activity-to-service");
		Log.v("CountService", activityValue);
	}
	@Override
	public boolean onUnbind(Intent intent) {
		// TODO Auto-generated method stub
		Log.v("CountService", "on unbind");
		return super.onUnbind(intent);
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		this.threadDisable = true;
		Log.v("CountService", "on destroy");
	}
	public int getCount() {
		return count;
	}
}
```
context.bindService() 
```  
public interface ICountService {
	public abstract int getCount();
}
```
java代码：
```  
public class BindCountService extends Service {
	private int count;
	private boolean threadDisable;
	private String activityValue = null;
	@Override
	public IBinder onBind(Intent intent) {
		Log.v("BindCountService", "on bind");
		// 接收activity传递过来的值
		Bundle b = intent.getExtras();
		activityValue = b.getString("activity-to-bindservice");
		Log.v("BindCountService", activityValue);
		return serviceBinder;
	}
	private ServiceBinder serviceBinder = new ServiceBinder();
	public class ServiceBinder extends Binder implements ICountService {
		@Override
		public int getCount() {
			return count;
		}
	}
	@Override
	public void onCreate() {
		super.onCreate();
		Log.v("BindCountService", "on create");
		new Thread(new Runnable() {
			@Override
			public void run() {
				while (!threadDisable) {
					try {
						Thread.sleep(1000);
					} catch (InterruptedException e) {
					}
					count++;
					Log.v("BindCountService", "Count is " + count);
				}
			}
		}).start();
	}
	@Override
	public void onRebind(Intent intent) {
		// TODO Auto-generated method stub
		super.onRebind(intent);
		Log.v("BindCountService", "on rebind");
	}
	@Override
	public void onStart(Intent intent, int startId) {
		// TODO Auto-generated method stub
		super.onStart(intent, startId);
		Log.v("BindCountService", "on start");
	}
	@Override
	public boolean onUnbind(Intent intent) {
		// TODO Auto-generated method stub
		Log.v("BindCountService", "on unbind");
		return super.onUnbind(intent);
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		this.threadDisable = true;
		Log.v("BindCountService", "on destroy");
	}
}
```
