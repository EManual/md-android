代码最后是一些 native 方法:
```  
private static native void nativeClassInit();//SensorManager 构造函数里调用
private static native int sensors_module_init();//SensorManager 构造函数里调用
private static native int sensors_module_get_next_sensor(Sensor sensor, intnext);
//SensorManager 构造函数里调用
// Used within this module from outside SensorManager, don't make private
static native int sensors_data_init();//SensorThread 构造里调用
static native int sensors_data_uninit();//SensorThread 析构里调用
static native int sensors_data_open(FileDescriptor fd); //SensorThread 的 run()循环调用
static native int sensors_data_close();//SensorThread 的 run()循环调用
static native int sensors_data_poll(float[] values, int[] status, long[] timestamp);
//SensorThread的run()循环调用
//SensorManager 与 IsensorService 的关系
//SensorManager 调用 IsensorService 其实只是调用了 service 的方法来控制 thread 是 Lock
void startLocked(ISensorService service) {
	ParcelFileDescriptor fd = service.getDataChanel();
}
```
或者打开　
```  　
mSensorService.enableSensor(l, name, handle, delay);
IsensorService的实例是这么获得的
mSensorService = ISensorService.Stub.asInterface(ServiceManager.getService(Context.SENSOR_SERVICE));
```
sensorService 是通过 aidl 定义的
```  
interface ISensorService{
	ParcelFileDescriptor getDataChanel();
	boolean enableSensor(IBinder listener, String name, int sensor, int enable);
}
class SensorService extends ISensorService.Stub {
}
```
service最终被manager调到走的是android的标准流程我们不care,我们想知道的其实就是enableSensor的实现
```  
if (enable == SENSOR_DISABLE) {
	mBatteryStats.noteStopSensor(uid, sensor);
} else {
	mBatteryStats.noteStartSensor(uid, sensor);
}
//看是不是能打开 sensor
if (enable!=SENSOR_DISABLE && !_sensors_control_activate(sensor, true)) {
	Log.w(TAG, "could not enable sensor " + sensor);
	return false;
}
//如果 sensor 打开了 我们要监听状态还要对外面报告状态变化
if (l == null && enable!=SENSOR_DISABLE) {
	l = new Listener(binder);
	binder.linkToDeath(l, 0);
	mListeners.add(l);
	mListeners.notify();
}
//如果 sensor 被关闭了 我们要取消监听并且告诉外面关闭了传感
if (enable != SENSOR_DISABLE) {
	l.addSensor(sensor, enable);
} else {
	l.removeSensor(sensor);
	deactivateIfUnused(sensor);
	if (l.mSensors == 0) {
		mListeners.remove(l);
		binder.unlinkToDeath(l, 0);
		mListeners.notify();
	}
}
```