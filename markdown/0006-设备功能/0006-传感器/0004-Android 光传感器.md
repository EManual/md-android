光传感器主要用来检测手机周围光的强度，与其他传感器不同的是，该传感器只读取一个数值即手机周围光的强度，且单位为勒克斯（lux）。
光传感器的开发与之前介绍过的各种传感器的开发步骤基本相同，只是监测的是SENSOR_LIGHT，即捕捉光的强度。如果对之前的案例进行更改，使其检测光的强度，则只需要改变监听器对象及注册监听的方法即可，代码如下所示。
```  
private SensorListener mySensorListener = new SensorListener() {
	@Override
	public void onAccuracyChanged(int sensor, int accuracy) {
	}
	// 重写onAccuracyChanged方法
	@Override
	public void onSensorChanged(int sensor, float[] values) {
		// 重写onSensorChanged方法
		if (sensor == SensorManager.SENSOR_LIGHT) {
			// 只检查光强度的变化
			myTextView1.setText("光的强度为：" + values[0]);
			// 将光的强度显示到TextView
		}
	}
};
@Override
protected void onResume() {
	// 重写的onResume方法
	mySensorManager.registerListener(
	// 注册监听
	mySensorListener,
	// 监听器SensorListener对象
	SensorManager.SENSOR_LIGHT,
	// 传感器的类型为光的强度
	SensorManager.SENSOR_DELAY_UI
	// 频率 
	);
	super.onResume();
}
```
第6行判断是否为光强度改变事件，只对光强度改变事件进行处理，得到光强并显示到屏幕中。光传感器得到的数据只有一个，而并不像其他传感器那样得到的是X、Y、Z三个方向上的分量。
在第15行注册监听时，传入"SensorManager.SENSOR_LIGHT"来通知系统只注册光传感器。
提示：除了之前介绍过的各种传感器外，Android系统还提供了一些其他的传感器，如压力传感器Pressure、距离传感器Proximity等，因本书篇幅有限，而且这些传感器并不常用，所以在此不再进行介绍，有兴趣的读者可以查阅相关资料进行学习。