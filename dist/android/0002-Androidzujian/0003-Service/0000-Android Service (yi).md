#### 一、Service的概念
Service是Android程序中四大基础组件之一，它和Activity一样都是Context的子类，只不过它没有UI界面，是在后台运行的组件。
#### 二、Service的生命周期
Service对象不能自己启动，需要通过某个Activity、Service或者其他Context对象来启动.启动的方法有两种，Context.startService和Context.bindService().两种方式的生命周期是不同的，具体如下所示。
使用context.startService()启动Service是会会经历：
```  
context.startService()  ->onCreate() ->onStart() ->Service running
context.stopService()  ->onDestroy() ->Service stop
```
如果Service还没有运行，则android先调用onCreate()然后调用onStart()；如果Service已经运行，则只调用onStart()，所以一个Service的onStart方法可能会重复调用多次。
stopService的时候直接onDestroy，如果是调用者自己直接退出而没有调用stopService的话，Service会一直在后台运行。该Service的调用者再启动起来后可以通过stopService关闭Service。
所以调用startService的生命周期为：onCreate --> onStart(可多次调用) --> onDestroy
使用使用context.bindService()启动Service会经历：
```  
context.bindService()->onCreate()->onBind()->Service running
onUnbind() -> onDestroy() ->Service stop
```
onBind将返回给客户端一个IBind接口实例，IBind允许客户端回调服务的方法，比如得到Service运行的状态或其他操作.这个时候把调用者(Context，例如Activity)会和Service绑定在一起，Context退出了，Srevice就会调用onUnbind->onDestroy相应退出。
所以调用bindService的生命周期为：onCreate --> onBind(只一次，不可多次绑定) --> onUnbind --> onDestory。
在Service每一次的开启关闭过程中，只有onStart可被多次调用(通过多次startService调用)，其他onCreate，onBind，onUnbind，onDestory在一个生命周期中只能被调用一次。
context.startService() 
```  
import android.R;
import android.app.Activity;
import android.content.ComponentName;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class MyService extends Activity implements OnClickListener {
	 /** Called when the activity is first created. */
	Button buttonStart, buttonStop, buttonBind, buttonUnbind, buttonCount;
	private static final String TAG = "ServicesDemo";
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		buttonStart = (Button) findViewById(R.id.bt1);
		buttonStop = (Button) findViewById(R.id.bt2);
		buttonBind = (Button) findViewById(R.id.bt3);
		buttonUnbind = (Button) findViewById(R.id.bt4);
		buttonCount = (Button) findViewById(R.id.bt5);
		buttonStart.setOnClickListener(this);
		buttonStop.setOnClickListener(this);
		buttonBind.setOnClickListener(this);
		buttonUnbind.setOnClickListener(this);
		buttonCount.setOnClickListener(this);
	}
	// ////////////////////
	// 通过ServiceConnection的内部类实现来连接Service和Activity
	// /////////////////////
	private ICountService countService = null;
	private ServiceConnection serviceConnection = new ServiceConnection() {
		@Override
		public void onServiceConnected(ComponentName name, IBinder service) {
			countService = (ICountService) service;
			Log.v("CountService", "on serivce connected, count is
					+ countService.getCount());
		}
		@Override
		public void onServiceDisconnected(ComponentName name) {
			countService = null;
		}
	};
	@Override
	public void onClick(View v) {
		switch (v.getId()) {
		case R.id.bt1:
			Log.d(TAG, "onClick: starting srvice");
			// activity传递值给service，service里的onStart()方法有个intent参数，可以接收到activity传递的值
			Intent intent1 = new Intent();
			Bundle bundle1 = new Bundle();
			bundle1.putString("activity-to-service", "i come from activity");
			intent1.putExtras(bundle1);
			intent1.setClass(this, CountService.class);
			this.startService(intent1);
			break;
		case R.id.bt2:
			Log.d(TAG, "onClick: stopping srvice");
			this.stopService(new Intent(this, CountService.class));
			break;
		case R.id.bt3:
			Log.d(TAG, "onClick: bind srvice");
			// bindservice启动的时候不会调用onstart方法
			// activity传递值给service，service里的onbind()方法有个intent参数，可以接收到activity传递的值
			Intent intent2 = new Intent();
			Bundle bundle2 = new Bundle();
			bundle2.putString("activity-to-bindservice", "i come from activity");
			intent2.putExtras(bundle2);
			intent2.setClass(this, BindCountService.class);
			this.bindService(intent2, this.serviceConnection, BIND_AUTO_CREATE);
			break;
		case R.id.bt4:
			Log.d(TAG, "onClick: unbind srvice");
			this.unbindService(serviceConnection);
			break;
		case R.id.bt5:
			// 随时和service通信，让activity获取到service里count的值是多少
			if (countService != null)
				Log.d(TAG, "onClick: getCount=" + countService.getCount());
			break;
		}
	}
}
```