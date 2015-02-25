代码：
```  
struct sensors_data_device_t {
	struct hw_device_t common;
	[Tags]/**
	[Tags]* Prepare to read sensor data.
	[Tags]*
	[Tags]* This routine does NOT take ownership of the fd
	[Tags]* and must not close it. Typically this routine would
	[Tags]* use a duplicate of the fd parameter.
	[Tags]*
	[Tags]* @param fd from sensors_control_open.
	[Tags]*
	[Tags]* @return 0 if successful, < 0 on error
	[Tags]*/
	int (*data_open)(struct sensors_data_device_t *dev, int fd);
	[Tags]/**
	[Tags]* Caller has completed using the sensor data.
	[Tags]* The caller will not be blocked in sensors_data_poll
	[Tags]* when this routine is called.
	[Tags]*
	[Tags]* @return 0 if successful, < 0 on error
	[Tags]*/
	int (*data_close)(struct sensors_data_device_t *dev);
	[Tags]/**
	[Tags]* Return sensor data for one of the enabled sensors.
	[Tags]*
	[Tags]* @return sensor handle for the returned data, 0x7FFFFFFF when
	[Tags]* sensors_control_device_t.wake() is called and -errno on error
	[Tags]*
	[Tags]*/
	int (*poll)(struct sensors_data_device_t *dev,
	sensors_data_t* data);
};
```
最后一组函数
```  
[Tags]/** convenience API for opening and closing a device */
static inline int sensors_control_open(const struct hw_module_t* module,
struct sensors_control_device_t** device) {
	return module->methods->open(module,
	SENSORS_HARDWARE_CONTROL, (struct hw_device_t**)device);
}
static inline int sensors_control_close(struct sensors_control_device_t* device) {
	return device->common.close(&device->common);
}
static inline int sensors_data_open(const struct hw_module_t* module,
struct sensors_data_device_t** device) {
	return module->methods->open(module,
	SENSORS_HARDWARE_DATA, (struct hw_device_t**)device);
}
static inline int sensors_data_close(struct sensors_data_device_t* device) {
	return device->common.close(&device->common);
}
```