Android服务器端：
```  ：
import java.io.File;
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import android.app.Service;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.os.IBinder;
import android.util.Log;
 /**
  * 设置：android手机
  *  */
public class androidService extends Service {
	public static final String TAG = "TAG";
	public static Boolean mainThreadFlag = true;
	public static Boolean ioThreadFlag = true;
	ServerSocket serverSocket = null;
	final int SERVER_PORT = 10086;
	File testFile;
	private sysBroadcastReceiver sysBR;
	@Override
	public void onCreate() {
		super.onCreate();
		Log.v(TAG, Thread.currentThread().getName() + "---->" + " onCreate");
		/* 创建内部类sysBroadcastReceiver 并注册registerReceiver */
		sysRegisterReceiver();
		new Thread() {
			public void run() {
				doListen();
			};
		}.start();
	}
	private void doListen() {
		Log.d(TAG, Thread.currentThread().getName() + "---->
				+ " doListen() START");
		serverSocket = null;
		try {
			Log.d(TAG, Thread.currentThread().getName() + "---->
					+ " doListen() new serverSocket");
			serverSocket = new ServerSocket(SERVER_PORT);
			boolean mainThreadFlag = true;

			while (mainThreadFlag) {
				Log.d(TAG, Thread.currentThread().getName() + "---->
						+ " doListen() listen");
				Socket client = serverSocket.accept();
				new Thread(new ThreadReadWriterIOSocket(this, client)).start();
			}
		} catch (IOException e1) {
			Log.v(androidService.TAG, Thread.currentThread().getName()
					+ "---->" + "new serverSocket error");
			e1.printStackTrace();
		}
	}
	/* 创建内部类sysBroadcastReceiver 并注册registerReceiver */
	private void sysRegisterReceiver() {
		Log.v(TAG, Thread.currentThread().getName() + "---->
				+ "sysRegisterReceiver");
		sysBR = new sysBroadcastReceiver();
		/* 注册BroadcastReceiver */
		IntentFilter filter1 = new IntentFilter();
		/* 新的应用程序被安装到了设备上的广播 */
		filter1.addAction("android.intent.action.PACKAGE_ADDED");
		filter1.addDataScheme("package");
		filter1.addAction("android.intent.action.PACKAGE_REMOVED");
		filter1.addDataScheme("package");
		registerReceiver(sysBR, filter1);
	}
	/* 内部类：BroadcastReceiver 用于接收系统事件 */
	private class sysBroadcastReceiver extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
			String action = intent.getAction();
			if (action.equalsIgnoreCase("android.intent.action.PACKAGE_ADDED")) {
				// ReadInstalledAPP();
			} else if (action
					.equalsIgnoreCase("android.intent.action.PACKAGE_REMOVED")) {
				// ReadInstalledAPP();
			}
			Log.v(TAG, Thread.currentThread().getName() + "---->
					+ "sysBroadcastReceiver onReceive");
		}
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		// 关闭线程
		mainThreadFlag = false;
		ioThreadFlag = false;
		// 关闭服务器
		try {
			Log.v(TAG, Thread.currentThread().getName() + "---->
					+ "serverSocket.close()");
			serverSocket.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
		Log.v(TAG, Thread.currentThread().getName() + "---->
				+ "**************** onDestroy****************");
	}
	@Override
	public void onStart(Intent intent, int startId) {
		Log.d(TAG, Thread.currentThread().getName() + "---->" + " onStart()");
		super.onStart(intent, startId);
	}
	@Override
	public IBinder onBind(Intent arg0) {
		Log.d(TAG, " onBind");
		return null;
	}
}
```