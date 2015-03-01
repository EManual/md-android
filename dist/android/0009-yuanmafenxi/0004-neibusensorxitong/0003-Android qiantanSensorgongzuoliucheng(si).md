我们还是接上一篇帖子，做一个小例子让大家来看看。
```  
sensors_data_open(JNIEnv *env, jclass clazz, jobject fdo){
	jclass FileDescriptor = env->FindClass("java/io/FileDescriptor");
	jfieldID offset = env->GetFieldID(FileDescriptor, "descriptor", "I");
	int fd = env->GetIntField(fdo, offset);
	return sSensorDevice->data_open(sSensorDevice, fd); // doesn't take ownership of fd
}
```
调用到最后其实都是用的sSensorDevice的方法
#### 
static sensors_data_device_t* sSensorDevice = 0;
```
2.service 部分
```  
static JNINativeMethod gMethods[] = {
	{"_sensors_control_init", "()I", (void*) android_init },
	{"_sensors_control_open", "()Landroid/os/ParcelFileDescriptor;", (void*) android_open },
	{"_sensors_control_activate", "(IZ)Z", (void*) android_activate },
	{"_sensors_control_wake", "()I", (void*) android_data_wake },
	{"_sensors_control_set_delay","(I)I", (void*) android_set_delay },
};
```
然后上面的那些方法我就不一一贴了 给出一个例子 其实这么实现的
```  
android_activate(JNIEnv *env, jclass clazz, jint sensor, jboolean activate){
	int active = sSensorDevice->activate(sSensorDevice, sensor, activate);
	return (active<0) ? false : true;
}
```
所以最后调用的其实都是 sSensorDevice 的方法 其他的几个也是这样 sSensorDevice 是这个
```  
static sensors_control_device_t* sSensorDevice = 0;
```
3.继续追 终于到了硬件层了 最后一切的方法其实就在这里了
```  
struct sensors_control_device_t {
	struct hw_device_t common;
	 /**
	 * Returns the fd which will be the parameter to
	 * sensors_data_device_t::open_data().
	 * The caller takes ownership of this fd. This is intended to be
	 * passed cross processes.
	 *
	 * @return a fd if successful, < 0 on error
	 */
	int (*open_data_source)(struct sensors_control_device_t *dev);
	 /** Activate/deactivate one sensor.
	 *
	 * @param handle is the handle of the sensor to change.
	 * @param enabled set to 1 to enable, or 0 to disable the sensor.
	 *
	 * @return 0 on success, negative errno code otherwise
	 */
	int (*activate)(struct sensors_control_device_t *dev,int handle, int enabled);
	 /**
	 * Set the delay between sensor events in ms
	 *
	 * @return 0 if successful, < 0 on error
	 */
	int (*set_delay)(struct sensors_control_device_t *dev, int32_t ms);
	 /**
	 * Causes sensors_data_device_t.poll() to return -EWOULDBLOCK immediately.
	 */
	int (*wake)(struct sensors_control_device_t *dev);
};
```