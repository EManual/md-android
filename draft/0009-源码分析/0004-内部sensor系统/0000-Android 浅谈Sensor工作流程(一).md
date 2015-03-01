我们使用 sensor 接口一般只要注册一下 SensorListener 像下面这样
ApiDemo:
```  
mGraphView = new GraphView(this);
mSensorManager.registerListener(mGraphView,....);
```
这里的 listener 是因为 sensor 状态变化要产生变化的控件，然后在控件里重载onSensorChanged和onAccuracyChanged方法
```  
public void onSensorChanged(int sensor, float[] values)
public void onAccuracyChanged(int sensor, int accuracy)
```
SensorManager
Sensor主体代码和流程在frameworks/base/core/java/android/hardware/SensorManager.java里面
1.registerListener 其实是调用registerLegacyListener:
```  
public boolean registerListener(SensorListener listener, int sensors, int rate) {
	result = registerLegacyListener(...);
}
```
2. registerLegacyListener其实就是构造一个LegacyListener对象并将其加入HashMap中去
```  
private boolean registerLegacyListener(int legacyType, int type, SensorListener listener, int sensors, int rate)
{
	legacyListener = new LegacyListener(listener);
	mLegacyListenersMap.put(listener, legacyListener); //private HashMap< p>
	LegacyListener> mLegacyListenersMap
}		
```
3. LegacyListener做了2件事，一个是调用我们重载的那2个接口 还有一个就是将sensor的数据刷到我们的设备显示界面上去
```  
private class LegacyListener implements SensorEventListener {
	LegacyListener(SensorListener target) {
		mTarget = target;
		mSensors = 0;
	}
	public void onSensorChanged(SensorEvent event) {
		mapSensorDataToWindow();
		mTarget.onSensorChanged(...);//private SensorListener mTarget;
	}
	public void onAccuracyChanged(Sensor sensor, int accuracy) {
	}
}
```