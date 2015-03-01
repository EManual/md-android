另外还有一些唤醒和设置延迟的动作
```  
if (mListeners.size() == 0) {
	_sensors_control_wake();
}
if (minDelay >= 0) {
	_sensors_control_set_delay(minDelay);
}
```
从上面可以看出来 对于底层而言只要知道上层怎么调用传感的接口就好 所以最关心的还是我标绿的 native 方法 上层的传感流程其实比较简单 就是标准的 service 管理和 notify 流程
```  
private static native int _sensors_control_init();
private static native ParcelFileDescriptor _sensors_control_open();
private static native boolean _sensors_control_activate(int sensor, boolean activate)
private static native int _sensors_control_set_delay(int ms);
private static native int _sensors_control_wake();
```
1. manager 部分
frameworks/base/core/jni/android_hardware_SensorManager.cpp
先看一眼它的方法注册
```  
static JNINativeMethod gMethods[] = {
	{"nativeClassInit", "()V", (void*)nativeClassInit },
	{"sensors_module_init","()I", (void*)sensors_module_init },
	{"sensors_module_get_next_sensor","(Landroid/hardware/Sensor;I)I",(void*)sensors_module_get_next_sensor },
	{"sensors_data_init", "()I", (void*)sensors_data_init },
	{"sensors_data_uninit", "()I", (void*)sensors_data_uninit },
	{"sensors_data_open", "(Ljava/io/FileDescriptor;)I", (void*)sensors_data_open },
	{"sensors_data_close", "()I", (void*)sensors_data_close },
	{"sensors_data_poll", "([F[I[J)I", (void*)sensors_data_poll },
};	
```