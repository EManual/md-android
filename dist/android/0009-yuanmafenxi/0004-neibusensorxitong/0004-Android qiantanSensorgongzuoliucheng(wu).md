代码：
```  
struct sensors_data_device_t {
	struct hw_device_t common;
	 /**
	 * Prepare to read sensor data.
	 *
	 * This routine does NOT take ownership of the fd
	 * and must not close it. Typically this routine would
	 * use a duplicate of the fd parameter.
	 *
	 * @param fd from sensors_control_open.
	 *
	 * @return 0 if successful, < 0 on error
	 */
	int (*data_open)(struct sensors_data_device_t *dev, int fd);
	 /**
	 * Caller has completed using the sensor data.
	 * The caller will not be blocked in sensors_data_poll
	 * when this routine is called.
	 *
	 * @return 0 if successful, < 0 on error
	 */
	int (*data_close)(struct sensors_data_device_t *dev);
	 /**
	 * Return sensor data for one of the enabled sensors.
	 *
	 * @return sensor handle for the returned data, 0x7FFFFFFF when
	 * sensors_control_device_t.wake() is called and -errno on error
	 *
	 */
	int (*poll)(struct sensors_data_device_t *dev,
	sensors_data_t* data);
};
```
最后一组函数
```  
 /** convenience API for opening and closing a device */
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