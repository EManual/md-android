原以为android切换屏幕（比如从竖屏转为横屏），并不会销毁activity的，只是改变内部的显示内容。做了个实验，在helloworld的android项目上增加了各个生命周期的方法和日志。见：
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
public class HelloActivity extends Activity {
	private static final String TAG = "hv.demo";
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Log.v(TAG, "@@@ on create");
	}
	@Override
	protected void onStart() {
		super.onStart();
		Log.v(TAG, "@@@ on start");
	}
	@Override
	protected void onResume() {
		super.onResume();
		Log.v(TAG, "@@@ on resume");
	}
	@Override
	protected void onPause() {
		super.onPause();
		Log.v(TAG, "@@@ on pause");
	}
	@Override
	protected void onStop() {
		super.onStop();
		Log.v(TAG, "@@@ on stop");
	}
	@Override
	protected void onDestroy() {
		super.onDestroy();
		Log.v(TAG, "@@@ on destroy");
	}
}
```
结果发现，当改变屏幕方向后，日志类似这样：
效果图：
![img](P)  
标红线的地方，是当时转动了屏幕。这下大家就都看明白了吧。希望这个能帮助大家在开发中遇到的问题。