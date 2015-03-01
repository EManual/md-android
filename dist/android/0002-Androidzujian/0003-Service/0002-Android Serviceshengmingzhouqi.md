首先创建——开始——销毁，整个流程。真的非常简单，先让大家看一下图先。

![img](http://emanual.github.io/md-android/img/component_service/03_service.jpg) 

根据上面的按钮所得到方法，大家可以在LogCat中增加一个标签，立即可以看到
```  
import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.util.Log;
public class ServiceLeft extends Service {
	private static final String TAG = "ServiceLeft";
	@Override
	public IBinder onBind(Intent arg0) {
		return null;
	}
	@Override
	public void onCreate() {
		// //进行创建
		Log.i(TAG, "onCreate");
		super.onCreate();
	}
	@Override
	public void onDestroy() {
		// 进行销毁
		Log.i(TAG, "onDestroy");
		super.onDestroy();
	}
	@Override
	public void onStart(Intent intent, int startId) {
		// 进行开始
		Log.i(TAG, "onStart");
		super.onStart(intent, startId);
	}
}
```
Java代码：
```  
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class Main extends Activity implements OnClickListener {
	private Button llbstatr;
	private Button llbstop;
	private Intent serviceIntent;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		llbstatr = (Button) findViewById(R.id.llbstart);
		llbstop = (Button) findViewById(R.id.llbstop);
		llbstatr.setOnClickListener(this);// 绑定器
		llbstop.setOnClickListener(this);
		serviceIntent = new Intent(this, ServiceLeft.class);
		// 跳转ServiceLeft生命周期并且调用方法
	}
	@Override
	public void onClick(View v) {
		// 根据按钮，触发事件
		switch (v.getId()) {
		case R.id.llbstart:
			startService(serviceIntent);
			break;
		case R.id.llbstop:
			stopService(serviceIntent);
			break;
		}
	}
}
```