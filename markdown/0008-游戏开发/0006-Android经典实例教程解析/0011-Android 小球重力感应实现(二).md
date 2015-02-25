3.注册SensorEventListener
使用SensorMannager调用getDefaultSensor(Sensor.TYPE_ACCELEROMETER)方法拿到加速重力感应的Sensor对象。因为本章我们讨论重力加速度传感器所以参数为Sensor.TYPE_ACCELEROMETER，如果须要拿到其它的传感器须要传入对应的名称。使用SensorMannager调用registerListener()方法来注册，第三个参数是检测的灵敏精确度根据不同的需求来选择精准度，游戏开发建议使用  SensorManager.SENSOR_DELAY_GAME。
```  
mSensorMgr = (SensorManager) getSystemService(SENSOR_SERVICE); 
mSensor = mSensorMgr.getDefaultSensor(Sensor.TYPE_ACCELEROMETER); 
// 注册listener，第三个参数是检测的精确度 
//SENSOR_DELAY_FASTEST 最灵敏 因为太快了没必要使用 
//SENSOR_DELAY_GAME 游戏开发中使用 
//SENSOR_DELAY_NORMAL 正常速度 
//SENSOR_DELAY_UI 最慢的速度 
mSensorMgr.registerListener(this, mSensor, SensorManager.SENSOR_DELAY_GAME); 
```
重力感应简单速度计算的方式。 每次摇晃手机计算出 X轴 Y轴 Z轴的重力分量可以将它们记录下来，然后每次摇晃的重力分量和之前的重力分量可以做一个对比 利用差值和时间就可以计算出他们的移动速度。
```  
private SensorManager sensorMgr; 
Sensor sensor = sensorMgr.getDefaultSensor(Sensor.TYPE_ACCELEROMETER); 
//保存上一次 x y z 的坐标 
float bx = 0; 
float by = 0; 
float bz = 0; 
long btime = 0;//这一次的时间 
sensorMgr = (SensorManager) getSystemService(SENSOR_SERVICE); 
SensorEventListener lsn = new SensorEventListener() { 
	public void onSensorChanged(SensorEvent e) { 
		float x = e.values[SensorManager.DATA_X]; 
		float y = e.values[SensorManager.DATA_Y]; 
		float z = e.values[SensorManager.DATA_Z]; 
		//在这里我们可以计算出 X Y Z的数值 下面我们就可以根据这个数值来计算摇晃的速度了 
		//我想大家应该都知道计算速度的公事 速度 = 路程/时间 
		//X轴的速度 
		float speadX = (x - bx) / (System.currentTimeMillis() - btime); 
		//y轴的速度 
		float speadY = (y - by) / (System.currentTimeMillis() - btime); 
		//z轴的速度 
		float speadZ = (z - bz) / (System.currentTimeMillis() - btime); 
		//这样简单的速度就可以计算出来 如果你想计算加速度 也可以 在运动学里，加速度a与速度， 
		//位移都有关系：Vt=V0+at，S=V0*t+1/2at^2， S=（Vt^2-V0^2）/(2a),根据这些信息也可以求解a。 
		//这里就不详细介绍了 公事 应该初中物理课老师就教了呵呵~~ 
		bx = x;
		by = y;
		bz = z;
		btime = System.currentTimeMillis();
	} 
	public void onAccuracyChanged(Sensor s, int accuracy) { 
	} 
}; 
// 注册listener，第三个参数是检测的精确度 
sensorMgr.registerListener(lsn, sensor, SensorManager.SENSOR_DELAY_GAME); 
```
![img](P)  