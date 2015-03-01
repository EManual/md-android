经过几天的研究，最终确定了手机蓝牙通信其实就是Socket编程，在经过一番编写和调试，昨晚终于大功告成！
下面开始介绍Android手机端控制程序的编写：
首先打开Eclipse，当然之前的Java开发环境和安卓开发工具自己得先配置好，这里就不多说了，网上教程一大摞。
然后新建一个Android项目，修改布局文件main.xml，
```  
<?xml version="1.0" encoding="utf-8"？>
<AbsoluteLayout
	android：id="@+id/widget0"
	android：layout_width="fill_parent
	android：layout_height="fill_parent""
	xmlns：android="http：//schemas.android.com/apk/res/android"
	>
	<Button
		android：id="@+id/btnF"
		android：layout_width="100px"
		android：layout_height="60px"
		android：text="前进"
		android：layout_x="130px"
		android：layout_y="62px"
		>
	</Button>
	<Button
		android：id="@+id/btnL"
		android：layout_width="100px"
		android：layout_height="60px"
		android：text="左转"
		android：layout_x="20px"
		android：layout_y="152px"
		>
	</Button>
	<Button
		android：id="@+id/btnR"
		android：layout_width="100px"
		android：layout_height="60px"
		android：text="右转"
		android：layout_x="240px"
		android：layout_y="152px"
		>
	</Button>
	<Button
		android：id="@+id/btnB"
		android：layout_width="100px"
		android：layout_height="60px"
		android：text="后退"
		android：layout_x="130px"
		android：layout_y="242px"
		>
	</Button>
	<Button
		android：id="@+id/btnS"
		android：layout_width="100px"
		android：layout_height="60px"
		android：text="停止"
		android：layout_x="130px"
		android：layout_y="152px"
		>
	</Button>
</AbsoluteLayout>

```
这个布局文件的效果就是如视频中所示的手机操作界面。
然后是权限声明，这一步不能少，否则将无法使用安卓手机的蓝牙功能。
权限声明如下：
打开AndroidManifest.xml文件，修改
```  
<？xml version="1.0" encoding="utf-8"？>
<manifest xmlns：android="http：//schemas.android.com/apk/res/android"
	package="net.androidchina.www"
	android：versionCode="1"
	android：versionName="1.0">
	<uses-permission android：name="android.permission.BLUETOOTH_ADMIN" />
	<uses-permission android：name="android.permission.BLUETOOTH" />
	<application android：icon="@drawable/icon" android：label="@string/app_name">
		<activity android：name=".ThinBTClient"
				  android：label="@string/app_name">
			<intent-filter>
				<action android：name="android.intent.action.MAIN" />
				<category android：name="android.intent.category.LAUNCHER" />
			</intent-filter>
		</activity>
	</application>
</manifest>
```
其中红色、加粗部分就是要添加的权限声明.
然后编写Activity中的执行代码，这些代码的作用就是发送指令，控制小车的运动.
```  
import java.io.IOException;
import java.io.OutputStream;
import java.util.UUID;
import android.R;
import android.app.Activity;
import android.bluetooth.BluetoothAdapter;
import android.bluetooth.BluetoothDevice;
import android.bluetooth.BluetoothSocket;
import android.os.Bundle;
import android.util.Log;
import android.view.MotionEvent;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
public class ThinBTClient extends Activity {
	private static final String TAG = "THINBTCLIENT";
	private static final boolean D = true;
	private BluetoothAdapter mBluetoothAdapter = null;
	private BluetoothSocket btSocket = null;
	private OutputStream outStream = null;
	Button mButtonF;
	Button mButtonB;
	Button mButtonL;
	Button mButtonR;
	Button mButtonS;
	private static final UUID MY_UUID = UUID
			.fromString("00001101-0000-1000-8000-00805F9B34FB");
	private static String address = "00:11:03:21:00:43"; // <==要连接的蓝牙设备MAC地址
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 前进
		mButtonF = (Button) findViewById(R.id.btnF);
		mButtonF.setOnTouchListener(new Button.OnTouchListener() {
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				// TODO Auto-generated method stub
				String message;
				byte[] msgBuffer;
				int action = event.getAction();
				switch (action) {
				case MotionEvent.ACTION_DOWN:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "1";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				case MotionEvent.ACTION_UP:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "0";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				}
				return false;
			}
		});
		// 后退
		mButtonB = (Button) findViewById(R.id.btnB);
		mButtonB.setOnTouchListener(new Button.OnTouchListener() {
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				// TODO Auto-generated method stub
				String message;
				byte[] msgBuffer;
				int action = event.getAction();
				switch (action) {
				case MotionEvent.ACTION_DOWN:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "3";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				case MotionEvent.ACTION_UP:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "0";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				}
				return false;
			}
		});
		// 左转
		mButtonL = (Button) findViewById(R.id.btnL);
		mButtonL.setOnTouchListener(new Button.OnTouchListener() {
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				String message;
				byte[] msgBuffer;
				int action = event.getAction();
				switch (action) {
				case MotionEvent.ACTION_DOWN:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "2";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				case MotionEvent.ACTION_UP:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "0";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				}
				return false;
			}
		});
		// 右转
		mButtonR = (Button) findViewById(R.id.btnR);
		mButtonR.setOnTouchListener(new Button.OnTouchListener() {
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				String message;
				byte[] msgBuffer;
				int action = event.getAction();
				switch (action) {
				case MotionEvent.ACTION_DOWN:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "4";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				case MotionEvent.ACTION_UP:
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
					message = "0";
					msgBuffer = message.getBytes();
					try {
						outStream.write(msgBuffer);
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Exception during write.", e);
					}
					break;
				}
				return false;
			}
		});
		// 停止
		mButtonS = (Button) findViewById(R.id.btnS);
		mButtonS.setOnTouchListener(new Button.OnTouchListener() {
			@Override
			public boolean onTouch(View v, MotionEvent event) {
				if (event.getAction() == MotionEvent.ACTION_DOWN)
					try {
						outStream = btSocket.getOutputStream();
					} catch (IOException e) {
						Log.e(TAG, "ON RESUME: Output stream creation failed.",
								e);
					}
				String message = "0";
				byte[] msgBuffer = message.getBytes();
				try {
					outStream.write(msgBuffer);
				} catch (IOException e) {
					Log.e(TAG, "ON RESUME: Exception during write.", e);
				}
				return false;
			}
		});
		if (D)
			Log.e(TAG, "+++ ON CREATE +++");
		mBluetoothAdapter = BluetoothAdapter.getDefaultAdapter();
		if (mBluetoothAdapter == null) {
			Toast.makeText(this, "Bluetooth is not available.",
					Toast.LENGTH_LONG).show();
			finish();
			return;
		}
		if (!mBluetoothAdapter.isEnabled()) {
			Toast.makeText(this,
					"Please enable your Bluetooth and re-run this program.",
					Toast.LENGTH_LONG).show();
			finish();
			return;
		}
		if (D)
			Log.e(TAG, "+++ DONE IN ON CREATE, GOT LOCAL BT ADAPTER +++");
	}
	@Override
	public void onStart() {
		super.onStart();
		if (D)
			Log.e(TAG, "++ ON START ++");
	}
	@Override
	public void onResume() {
		super.onResume();
		if (D) {
			Log.e(TAG, "+ ON RESUME +");
			Log.e(TAG, "+ ABOUT TO ATTEMPT CLIENT CONNECT +");
		}
		BluetoothDevice device = mBluetoothAdapter.getRemoteDevice(address);
		try {
			btSocket = device.createRfcommSocketToServiceRecord(MY_UUID);
		} catch (IOException e) {
			Log.e(TAG, "ON RESUME: Socket creation failed.", e);
		}
		mBluetoothAdapter.cancelDiscovery();
		try {
			btSocket.connect();
			Log.e(TAG,
					"ON RESUME: BT connection established, data transfer link open.");
		} catch (IOException e) {
			try {
				btSocket.close();
			} catch (IOException e2) {
				Log.e(TAG,
						"ON RESUME: Unable to close socket during connection failure",
						e2);
			}
		}
		// Create a data stream so we can talk to server.
		if (D)
			Log.e(TAG, "+ ABOUT TO SAY SOMETHING TO SERVER +");
		/*
		  * try { outStream = btSocket.getOutputStream(); } catch (IOException e)
		  * { Log.e(TAG, "ON RESUME: Output stream creation failed.", e); }
		  * String message = "1"; byte[] msgBuffer = message.getBytes(); try {
		  * outStream.write(msgBuffer); } catch (IOException e) { Log.e(TAG,
		  * "ON RESUME: Exception during write.", e); }
		  */
	}
	@Override
	public void onPause() {
		super.onPause();
		if (D)
			Log.e(TAG, "- ON PAUSE -");
		if (outStream != null) {
			try {
				outStream.flush();
			} catch (IOException e) {
				Log.e(TAG, "ON PAUSE: Couldn't flush output stream.", e);
			}
		}
		try {
			btSocket.close();
		} catch (IOException e2) {
			Log.e(TAG, "ON PAUSE: Unable to close socket.", e2);
		}
	}
	@Override
	public void onStop() {
		super.onStop();
		if (D)
			Log.e(TAG, "-- ON STOP --");
	}
	@Override
	public void onDestroy() {
		super.onDestroy();
		if (D)
			Log.e(TAG, "--- ON DESTROY ---");
	}
}
```